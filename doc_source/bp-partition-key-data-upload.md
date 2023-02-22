# Distributing write activity efficiently during data upload<a name="bp-partition-key-data-upload"></a>

Typically, when you load data from other data sources, Amazon DynamoDB partitions your table data on multiple servers\. You get better performance if you upload data to all the allocated servers simultaneously\.

For example, suppose that you want to upload user messages to a DynamoDB table that uses a composite primary key with `UserID` as the partition key and `MessageID` as the sort key\.

When you upload the data, one approach you can take is to upload all message items for each user, one user after another:


****  

| UserID | MessageID | 
| --- | --- | 
| U1 | 1 | 
| U1 | 2 | 
| U1 | \.\.\. | 
| U1 | \.\.\. up to 100 | 
| U2 | 1 | 
| U2 | 2 | 
| U2 | \.\.\. | 
| U2 | \.\.\. up to 200 | 

The problem in this case is that you are not distributing your write requests to DynamoDB across your partition key values\. You are taking one partition key value at a time and uploading all of its items before going to the next partition key value and doing the same\.

Behind the scenes, DynamoDB is partitioning the data in your table across multiple servers\. To fully use all the throughput capacity that is provisioned for the table, you must distribute your workload across your partition key values\. By directing an uneven amount of upload work toward items that all have the same partition key value, you are not fully using all the resources that DynamoDB has provisioned for your table\.

You can distribute your upload work by using the sort key to load one item from each partition key value, then another item from each partition key value, and so on: 


****  

| UserID | MessageID | 
| --- | --- | 
| U1 | 1 | 
| U2 | 1 | 
| U3 | 1 | 
| \.\.\. | \.\.\. | 
| U1 | 2 | 
| U2 | 2 | 
| U3 | 2 | 
| \.\.\. | \.\.\. | 

Every upload in this sequence uses a different partition key value, keeping more DynamoDB servers busy simultaneously and improving your throughput performance\. 