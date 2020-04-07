# Time to Live: How It Works<a name="howitworks-ttl"></a>

When Time to Live \(TTL\) is enabled on a table in Amazon DynamoDB, a background job checks the TTL attribute of items to determine whether they are expired\. 

DynamoDB compares the current time, in [epoch time format](https://en.wikipedia.org/wiki/Unix_time), to the value stored in the user\-defined `Number` attribute of an item\. If the attribute’s value is in the epoch time format, is less than the current time, and is not older than 5 years, the item is deleted\. Processing takes place automatically, in the background, and doesn't affect read or write traffic to the table\. In addition, deletes performed via TTL are not counted towards capacity units or request units\. TTL deletes are available at no additional cost\.

**Note**  
The epoch time format is the number of seconds elapsed since 12:00:00 AM January 1, 1970 UTC\. For example, if you needed to represent the date `15 January 2020 19:26:52 GMT`, you would put `1579116412` as the value of a number attribute on an item\.

**Important**  
DynamoDB typically deletes expired items within 48 hours of expiration\. The exact duration within which an item truly gets deleted after expiration is specific to the nature of the workload and the size of the table\. Items that have expired and have not been deleted still appear in reads, queries, and scans\. These items can still be updated, and successful updates to change or remove the expiration attribute are honored\.

As items are deleted, they are removed from any local secondary index and global secondary index in the same eventually consistent way as a standard delete operation\.

For example, consider a table named `SessionData` that tracks the session history of users\. Each item in `SessionData` is identified by a partition key \(`UserName`\) and a sort key \(`SessionId`\)\. Additional attributes like `UserName`, `SessionId`, `CreationTime`, and `ExpirationTime` track the session information\. The `ExpirationTime` attribute is set as the TTL attribute \(not all of the attributes are shown\)\. 


**SessionData**  

| UserName | SessionId | CreationTime | ExpirationTime \(TTL\) | SessionInfo | … | 
| --- | --- | --- | --- | --- | --- | 
| user1 | 74686572652773 | 1461931200 | 1461938400 | \{JSON Document\} | \.\.\. | 
| user2 | 6e6f7468696e67  | 1461920400 | 1461927600 | \{JSON Document\} | \.\.\. | 
| user3 | 746f2073656520 | 1461922200 | 1461929400 | \{JSON Document\} | \.\.\. | 
| user4  | 68657265212121 | 1461925380 | 1461932580 | \{JSON Document\}  | … | 
| user5 | 6e6572642e2e2e | 1461920400 | 1461927600 | \{JSON Document\} | \.\.\. | 
| \.\.\.  | \.\.\.  | \.\.\.  |  \.\.\. |  \.\.\. | … | 

In this example, each item has an `ExpirationTime` attribute value set when it is created\. Consider the following record\.


**SessionData**  

| UserName | SessionId | CreationTime | ExpirationTime \(TTL\) | SessionInfo | … | 
| --- | --- | --- | --- | --- | --- | 
| user1 | 74686572652773 | 1461931200 | 1461938400 | \{JSON Document\} | \.\.\. | 

In this example, the item `CreationTime` is set to Friday, April 29 12:00 PM UTC 2016, and the `ExpirationTime` is set 2 hours later at Friday, April 29 2:00 PM UTC 2016\. The item expires when the current time, in epoch format, is greater than the time in the `ExpirationTime` attribute\. In this case, the item with the key `{ Username: user1, SessionId: 74686572652773 }` expires after 2:00 PM \(1461938400\)\.

**Note**  
Due to the potential delay between expiration and deletion time, you might get expired items when you query for items\. If you don’t want to view expired items when you issue a read request, you must filter them out\. To do this, use a *filter expression* that returns only items where the Time to Live expiration value is greater than the current time in epoch format\. For more information, see [Filter Expressions for Query](Query.md#Query.FilterExpression) and [Filter Expressions for Scan](Scan.md#Scan.FilterExpression)\. 