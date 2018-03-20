# Time To Live: How It Works<a name="howitworks-ttl"></a>

When Time To Live is enabled on a table, a background job checks the TTL attribute of items to see if they are expired\. 

TTL compares the current time in epoch time format to the time stored in the Time To Live attribute of an item\. If the epoch time value stored in the attribute is less than the current time, the item is marked as expired and subsequently deleted\.

**Note**  
 The epoch time format is the number of seconds elapsed since 12:00:00 AM January 1st, 1970 UTC\. 

DynamoDB deletes expired items on a best\-effort basis to ensure availability of throughput for other data operations\.  

**Important**  
 DynamoDB typically deletes expired items within 48 hours of expiration\. The exact duration within which an item truly gets deleted after expiration is specific to the nature of the workload and the size of the table\. Items that have expired and not been deleted will still show up in reads, queries, and scans\.

As items are deleted, they are removed from any Local Secondary Index and Global Secondary Index immediately in the same eventually consistent way as a standard delete operation\.

For example, consider a table named *SessionData* that tracks the session history of users\. Each item in *SessionData* is identified by a partition key \(*UserName*\) and a sort key \(*SessionId*\)\. Additional attributes like *UserName*,*SessionId*, *CreationTime* and *ExpirationTime* track the session information\.

The following diagram shows how the items in the table would be organized\. The *ExpirationTime* attribute is set as the Time To Live \(TTL\) attribute\. \(Not all of the attributes are shown\) 


**SessionData**  

| UserName | SessionId | CreationTime | ExpirationTime \(TTL\) | SessionInfo | … | 
| --- | --- | --- | --- | --- | --- | 
| user1 | 74686572652773 | 1461931200 | 1461938400 | \{JSON Document\} | \.\.\. | 
| user2 | 6e6f7468696e67  | 1461920400 | 1461927600 | \{JSON Document\} | \.\.\. | 
| user3 | 746f2073656520 | 1461922200 | 1461929400 | \{JSON Document\} | \.\.\. | 
| user4  | 68657265212121 | 1461925380 | 1461932580 | \{JSON Document\}  | … | 
| user5 | 6e6572642e2e2e | 1461920400 | 1461927600 | \{JSON Document\} | \.\.\. | 
| \.\.\.  | \.\.\.  | \.\.\.  |  \.\.\. |  \.\.\. | … | 

In this example each item has an *ExpirationTime* attribute value set when it is created\. Consider the first record: 


**SessionData**  

| UserName | SessionId | CreationTime | ExpirationTime \(TTL\) | SessionInfo | … | 
| --- | --- | --- | --- | --- | --- | 
| user1 | 74686572652773 | 1461931200 | 1461938400 | \{JSON Document\} | \.\.\. | 

In this example, the item *CreationTime* is set to Friday, April 29 12:00 PM UTC 2016 and the *ExpirationTime* is set 2 hours later at Friday, April 29 2:00 PM UTC 2016\. The item will expire when the current time, in epoch format, is greater than the time in the *ExpirationTime* attribute\. In this case, the item with the key `{ Username: user1, SessionId: 74686572652773 }` will expire after 2:00 PM \(1461938400\)\.

**Note**  
Due to the potential delay between expiration and deletion time, you might get expired items when you query for items\. If you don’t want to view expired items when you issue a read request, you should filter them out\. You can do this by using a *filter expression* that returns only items where the Time To Live expiration value is greater than the current time in epoch format\. For more information, see [Filter Expressions for *Query*](Query.md#Query.FilterExpression) and [Filter Expressions for *Scan*](Scan.md#Scan.FilterExpression)\. 
