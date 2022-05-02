# Improving Data Access with Secondary Indexes<a name="SecondaryIndexes"></a>

**Topics**
+ [Using Global Secondary Indexes in DynamoDB](GSI.md)
+ [Local Secondary Indexes](LSI.md)

Amazon DynamoDB provides fast access to items in a table by specifying primary key values\. However, many applications might benefit from having one or more secondary \(or alternate\) keys available, to allow efficient access to data with attributes other than the primary key\. To address this, you can create one or more secondary indexes on a table and issue `Query` or `Scan` requests against these indexes\.

A *secondary index* is a data structure that contains a subset of attributes from a table, along with an alternate key to support `Query` operations\. You can retrieve data from the index using a `Query`, in much the same way as you use `Query` with a table\. A table can have multiple secondary indexes, which give your applications access to many different query patterns\.

**Note**  
You can also `Scan` an index, in much the same way as you would `Scan` a table\.

Every secondary index is associated with exactly one table, from which it obtains its data\. This is called the *base table* for the index\. When you create an index, you define an alternate key for the index \(partition key and sort key\)\. You also define the attributes that you want to be *projected*, or copied, from the base table into the index\. DynamoDB copies these attributes into the index, along with the primary key attributes from the base table\. You can then query or scan the index just as you would query or scan a table\. 

Every secondary index is automatically maintained by DynamoDB\. When you add, modify, or delete items in the base table, any indexes on that table are also updated to reflect these changes\.

DynamoDB supports two types of secondary indexes:
+ **[Global secondary index](GSI.html) — **An index with a partition key and a sort key that can be different from those on the base table\. A global secondary index is considered "global" because queries on the index can span all of the data in the base table, across all partitions\. A global secondary index is stored in its own partition space away from the base table and scales separately from the base table\.
+ **[Local secondary index](LSI.html) — **An index that has the same partition key as the base table, but a different sort key\. A local secondary index is "local" in the sense that every partition of a local secondary index is scoped to a base table partition that has the same partition key value\.

You should consider your application's requirements when you determine which type of index to use\. The following table shows the main differences between a global secondary index and a local secondary index\.


****  

| Characteristic | Global Secondary Index | Local Secondary Index | 
| --- | --- | --- | 
| Key Schema | The primary key of a global secondary index can be either simple \(partition key\) or composite \(partition key and sort key\)\. | The primary key of a local secondary index must be composite \(partition key and sort key\)\. | 
| Key Attributes | The index partition key and sort key \(if present\) can be any base table attributes of type string, number, or binary\. | The partition key of the index is the same attribute as the partition key of the base table\. The sort key can be any base table attribute of type string, number, or binary\. | 
| Size Restrictions Per Partition Key Value | There are no size restrictions for global secondary indexes\. | For each partition key value, the total size of all indexed items must be 10 GB or less\. | 
| Online Index Operations | Global secondary indexes can be created at the same time that you create a table\. You can also add a new global secondary index to an existing table, or delete an existing global secondary index\. For more information, see [Managing Global Secondary Indexes](GSI.OnlineOps.md)\.  | Local secondary indexes are created at the same time that you create a table\. You cannot add a local secondary index to an existing table, nor can you delete any local secondary indexes that currently exist\. | 
| Queries and Partitions | A global secondary index lets you query over the entire table, across all partitions\.  | A local secondary index lets you query over a single partition, as specified by the partition key value in the query\. | 
| Read Consistency | Queries on global secondary indexes support eventual consistency only\. | When you query a local secondary index, you can choose either eventual consistency or strong consistency\. | 
| Provisioned Throughput Consumption | Every global secondary index has its own provisioned throughput settings for read and write activity\. Queries or scans on a global secondary index consume capacity units from the index, not from the base table\. The same holds true for global secondary index updates due to table writes\. | Queries or scans on a local secondary index consume read capacity units from the base table\. When you write to a table, its local secondary indexes are also updated; these updates consume write capacity units from the base table\. | 
| Projected Attributes | With global secondary index queries or scans, you can only request the attributes that are projected into the index\. DynamoDB does not fetch any attributes from the table\. | If you query or scan a local secondary index, you can request attributes that are not projected in to the index\. DynamoDB automatically fetches those attributes from the table\. | 

If you want to create more than one table with secondary indexes, you must do so sequentially\. For example, you would create the first table and wait for it to become `ACTIVE`, create the next table and wait for it to become `ACTIVE`, and so on\. If you try to concurrently create more than one table with a secondary index, DynamoDB returns a `LimitExceededException`\.

For each secondary index, you must specify the following:
+ The type of index to be created – either a global secondary index or a local secondary index\.
+ A name for the index\. The naming rules for indexes are the same as those for tables, as listed in [Service, Account, and Table Quotas in Amazon DynamoDB](ServiceQuotas.md)\. The name must be unique for the base table it is associated with, but you can use the same name for indexes that are associated with different base tables\.
+ The key schema for the index\. Every attribute in the index key schema must be a top\-level attribute of type `String`, `Number`, or `Binary`\. Other data types, including documents and sets, are not allowed\. Other requirements for the key schema depend on the type of index: 
  + For a global secondary index, the partition key can be any scalar attribute of the base table\. A sort key is optional, and it too can be any scalar attribute of the base table\.
  + For a local secondary index, the partition key must be the same as the base table's partition key, and the sort key must be a non\-key base table attribute\.
+ Additional attributes, if any, to project from the base table into the index\. These attributes are in addition to the table's key attributes, which are automatically projected into every index\. You can project attributes of any data type, including scalars, documents, and sets\.
+ The provisioned throughput settings for the index, if necessary:
  + For a global secondary index, you must specify read and write capacity unit settings\. These provisioned throughput settings are independent of the base table's settings\.
  + For a local secondary index, you do not need to specify read and write capacity unit settings\. Any read and write operations on a local secondary index draw from the provisioned throughput settings of its base table\.

For maximum query flexibility, you can create up to 20 global secondary indexes \(default quota\) and up to 5 local secondary indexes per table\. 

 The quota of global secondary indexes per table is five for the following AWS Regions:
+ AWS GovCloud \(US\-East\)
+ AWS GovCloud \(US\-West\)
+ Europe \(Stockholm\)

To get a detailed listing of secondary indexes on a table, use the `DescribeTable` operation\. `DescribeTable` returns the name, storage size, and item counts for every secondary index on the table\. These values are not updated in real time, but they are refreshed approximately every six hours\.

You can access the data in a secondary index using either the `Query` or `Scan` operation\. You must specify the name of the base table and the name of the index that you want to use, the attributes to be returned in the results, and any condition expressions or filters that you want to apply\. DynamoDB can return the results in ascending or descending order\.

When you delete a table, all of the indexes associated with that table are also deleted\.

For best practices, see [Best Practices for Using Secondary Indexes in DynamoDB](bp-indexes.md)\.