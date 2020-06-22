# General Guidelines for Secondary Indexes in DynamoDB<a name="bp-indexes-general"></a>

Amazon DynamoDB supports two types of secondary indexes:
+ **Global secondary index—**An index with a partition key and a sort key that can be different from those on the base table\. A global secondary index is considered "global" because queries on the index can span all of the data in the base table, across all partitions\. A global secondary index has no size limitations and has its own provisioned throughput settings for read and write activity that are separate from those of the table\.
+ **Local secondary index—**An index that has the same partition key as the base table, but a different sort key\. A local secondary index is "local" in the sense that every partition of a local secondary index is scoped to a base table partition that has the same partition key value\. As a result, the total size of indexed items for any one partition key value can't exceed 10 GB\. Also, a local secondary index shares provisioned throughput settings for read and write activity with the table it is indexing\.

Each table in DynamoDB can have up to 20 global secondary indexes \(default quota\) and 5 local secondary indexes\. 

For more information about the differences between global secondary indexes and local secondary indexes, see [Improving Data Access with Secondary Indexes](SecondaryIndexes.md)\.

In general, you should use global secondary indexes rather than local secondary indexes\. The exception is when you need strong consistency in your query results, which a local secondary index can provide but a global secondary index cannot \(global secondary index queries only support eventual consistency\)\.

The following are some general principles and design patterns to keep in mind when creating indexes in DynamoDB:

**Topics**
+ [Use Indexes Efficiently](#bp-indexes-general-efficiency)
+ [Choose Projections Carefully](#bp-indexes-general-projections)
+ [Optimize Frequent Queries to Avoid Fetches](#bp-indexes-general-fetches)
+ [Be Aware of Item\-Collection Size Limits When Creating Local Secondary Indexes](#bp-indexes-general-expanding-collections)

## Use Indexes Efficiently<a name="bp-indexes-general-efficiency"></a>

**Keep the number of indexes to a minimum\.** Don't create secondary indexes on attributes that you don't query often\. Indexes that are seldom used contribute to increased storage and I/O costs without improving application performance\. 

## Choose Projections Carefully<a name="bp-indexes-general-projections"></a>

Because secondary indexes consume storage and provisioned throughput, you should keep the size of the index as small as possible\. Also, the smaller the index, the greater the performance advantage compared to querying the full table\. If your queries usually return only a small subset of attributes, and the total size of those attributes is much smaller than the whole item, project only the attributes that you regularly request\.

If you expect a lot of write activity on a table compared to reads, follow these best practices:
+ Consider projecting fewer attributes to minimize the size of items written to the index\. However, this only applies if the size of projected attributes would otherwise be larger than a single write capacity unit \(1 KB\)\. For example, if the size of an index entry is only 200 bytes, DynamoDB rounds this up to 1 KB\. In other words, as long as the index items are small, you can project more attributes at no extra cost\.
+ Avoid projecting attributes that you know will rarely be needed in queries\. Every time you update an attribute that is projected in an index, you incur the extra cost of updating the index as well\. You can still retrieve non\-projected attributes in a `Query` at a higher provisioned throughput cost, but the query cost may be significantly lower than the cost of updating the index frequently\.
+ Specify `ALL` only if you want your queries to return the entire table item sorted by a different sort key\. Projecting all attributes eliminates the need for table fetches, but in most cases, it doubles your costs for storage and write activity\.

Balance the need to keep your indexes as small as possible against the need to keep fetches to a minimum, as explained in the next section\.

## Optimize Frequent Queries to Avoid Fetches<a name="bp-indexes-general-fetches"></a>

To get the fastest queries with the lowest possible latency, project all the attributes that you expect those queries to return\. In particular, if you query a local secondary index for attributes that are not projected, DynamoDB automatically fetches those attributes from the table, which requires reading the entire item from the table\. This introduces latency and additional I/O operations that you can avoid\.

Keep in mind that "occasional" queries can often turn into "essential" queries\. If there are attributes that you don't intend to project because you anticipate querying them only occasionally, consider whether circumstances might change and you might regret not projecting those attributes after all\.

For more information about table fetches, see [Provisioned Throughput Considerations for Local Secondary Indexes](LSI.md#LSI.ThroughputConsiderations)\.

## Be Aware of Item\-Collection Size Limits When Creating Local Secondary Indexes<a name="bp-indexes-general-expanding-collections"></a>

An *item collection* is all the items in a table and its local secondary indexes that have the same partition key\. No item collection can exceed 10 GB, so it's possible to run out of space for a particular partition key value\.

When you add or update a table item, DynamoDB updates all local secondary indexes that are affected\. If the indexed attributes are defined in the table, the local secondary indexes grow too\.

When you create a local secondary index, think about how much data will be written to it, and how many of those data items will have the same partition key value\. If you expect that the sum of table and index items for a particular partition key value might exceed 10 GB, consider whether you should avoid creating the index\.

If you can't avoid creating the local secondary index, you must anticipate the item collection size limit and take action before you exceed it\. For strategies on working within the limit and taking corrective action, see [Item Collection Size Limit](LSI.md#LSI.ItemCollections.SizeLimit)\.