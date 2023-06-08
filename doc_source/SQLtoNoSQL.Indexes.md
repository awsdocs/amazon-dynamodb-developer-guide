# Managing indexes<a name="SQLtoNoSQL.Indexes"></a>

Indexes give you access to alternate query patterns, and can speed up queries\. This section compares and contrasts index creation and usage in SQL and Amazon DynamoDB\.

Whether you are using a relational database or DynamoDB, you should be judicious with index creation\. Whenever a write occurs on a table, all of the table's indexes must be updated\. In a write\-heavy environment with large tables, this can consume large amounts of system resources\. In a read\-only or read\-mostly environment, this is not as much of a concern\. However, you should ensure that the indexes are actually being used by your application, and not simply taking up space\.

**Topics**
+ [Creating an index](SQLtoNoSQL.Indexes.Creating.md)
+ [Querying and scanning an index](SQLtoNoSQL.Indexes.QueryAndScan.md)