# How it works: DynamoDB Time to Live \(TTL\)<a name="howitworks-ttl"></a>

When enabling TTL on a DynamoDB table, you must identify a specific attribute name that the service will look for when determining if an item is eligible for expiration\. After you enable TTL on a table, a per\-partition scanner background process automatically and continuously evaluates the expiry status of items in the table\.

The scanner background process compares the current time, in [Unix epoch time format](https://en.wikipedia.org/wiki/Unix_time) in *seconds*, to the value stored in the user\-defined attribute of an item\. If the attribute is a `Number` data type, the attribute’s value is a timestamp in [Unix epoch time format](https://en.wikipedia.org/wiki/Unix_time) in *seconds*, and the timestamp value is older than the current time but not five years older or more \(in order to avoid a possible accidental deletion due to a malformed TTL value\), then the item is set to expired\. For specifics about formatting TTL attributes, see [Formatting an item’s TTL attribute](time-to-live-ttl-before-you-start.md#time-to-live-ttl-before-you-start-formatting)\. A second background process scans for expired items and deletes them\. Both processes take place automatically in the background, do not affect read or write traffic to the table, and do not have a monetary cost\.

As items are deleted from the table, two background operations happen simultaneously:
+ Items are removed from any local secondary index and global secondary index in the same way as a DeleteItem operation\. This operation comes at no extra cost\.
+ A delete operation for each item enters the DynamoDB Stream, but is tagged as a system delete and not a regular delete\. For more information about how to use this system delete, see [DynamoDB Streams and Time to Live](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/time-to-live-ttl-streams.html)\.

**Important**  
Depending on the size and activity level of a table, the actual delete operation of an expired item can vary\. Because TTL is meant to be a background process, the nature of the capacity used to expire and delete items via TTL is variable \(but free of charge\)\. TTL typically deletes expired items within 48 hours of expiration\.
Items that have expired, but haven’t yet been deleted by TTL, still appear in reads, queries, and scans\. If you do not want expired items in the result set, you must filter them out\. To do this, use a filter expression that returns only items where the Time to Live expiration value is greater than the current time in epoch format\. For more information, see [Filter expressions for scan](Scan.md#Scan.FilterExpression)\.
Items that are past their expiration, but have not yet been deleted can still be updated, and successful updates to change or remove the expiration attribute will be honored\.

You can monitor TTL rates on the CloudWatch metrics tab for a table, and see when and the rate at which items are deleted\.

![\[Console screenshot showing TTL and cloudwatch .\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/TTL_cloudwatch.png)

## Time to Live example<a name="howitworks-ttl-Example"></a>

For example, consider a table named SessionData that tracks the session history of users\. Each item in `SessionData` is identified by the partition key \(`UserName`\) and the sort key \(`SessionId`\)\. Additional attributes like `UserName`, `SessionId`, `CreationTime`, and `ExpirationTime` track the session information\. The `ExpirationTime` attribute is set as the TTL attribute on the table \(not all of the attributes on each item are shown\)\.


**SessionData**  

| UserName | SessionId | CreationTime | ExpirationTime \(TTL\) | SessionInfo | … | 
| --- | --- | --- | --- | --- | --- | 
| user1 | 74686572652773 | 1571820360 | 1571827560 | \{JSON Document\} | \.\.\. | 
| user2 | 6e6f7468696e67  | 1571820180 | 1571827380 | \{JSON Document\} | \.\.\. | 
| user3 | 746f2073656520 | 1571820923 | 1571828123 | \{JSON Document\} | \.\.\. | 
| user4  | 68657265212121 | 1571820683 | 1571827883 | \{JSON Document\}  | … | 
| user5 | 6e6572642e2e2e | 1571820743 | 1571831543 | \{JSON Document\} | \.\.\. | 
| \.\.\.  | \.\.\.  | \.\.\.  |  \.\.\. |  \.\.\. | … | 

In this example, each item has an `ExpirationTime` attribute value set when it is created\. Consider the following table item\.


**SessionData**  

| UserName | SessionId | CreationTime | ExpirationTime \(TTL\) | SessionInfo | … | 
| --- | --- | --- | --- | --- | --- | 
| user1 | 74686572652773 | 1571820360 | 1571827560 | \{JSON Document\} | \.\.\. | 

In this example, the item `CreationTime` is set to Wednesday, October 23 08:46 AM UTC 2019, and the `ExpirationTime` is set 2 hours later at Wednesday, October 23 10:46 AM UTC 2019\. The item expires when the current time, in epoch format, is greater than the time in the `ExpirationTime` attribute\. In this case, the item with the key `{ Username: user1, SessionId: 74686572652773}` expires 10:46 AM \(1571827560\)\.