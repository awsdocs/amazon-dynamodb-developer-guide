# Best Practices for Local Secondary Indexes<a name="GuidelinesForLSI"></a>


+ [Use Indexes Sparingly](#GuidelinesForLSI.NumberOfIndexes)
+ [Choose Projections Carefully](#GuidelinesForLSI.Projections)
+ [Optimize Frequent Queries To Avoid Fetches](#GuidelinesForLSI.TableFetches)
+ [Take Advantage of Sparse Indexes](#GuidelinesForLSI.SparseIndexes)
+ [Watch For Expanding Item Collections](#GuidelinesForLSI.ItemCollections)

This section covers some best practices for local secondary indexes\.

## Use Indexes Sparingly<a name="GuidelinesForLSI.NumberOfIndexes"></a>

Don't create local secondary indexes on attributes that you won't often query\. Creating and maintaining multiple indexes makes sense for tables that are updated infrequently and are queried using many different criteria\. Unused indexes, however, contribute to increased storage and I/O costs, and they do nothing for application performance\.

Avoid indexing tables, such as those used in data capture applications, that experience heavy write activity\. The cost of I/O operations required to maintain the indexes can be significant\. If you need to index the data in such a table, copy the data to another table with any necessary indexes, and query it there\.

## Choose Projections Carefully<a name="GuidelinesForLSI.Projections"></a>

Because local secondary indexes consume storage and provisioned throughput, you should keep the size of the index as small as possible\. Additionally, the smaller the index, the greater the performance advantage compared to querying the full table\. If your queries usually return only a small subset of attributes and the total size of those attributes is much smaller than the whole item, project only the attributes that you will regularly request\.

If you expect a lot of write activity on a table, compared to reads:

+ Consider projecting fewer attributes, which will minimize the size of items written to the index\. However, if these items are smaller than a single write capacity unit \(1 KB\), then there won't be any savings in terms of write capacity units\. For example, if the size of an index entry is only 200 bytes, DynamoDB rounds this up to 1 KB\. In other words, as long as the index items are small, you can project more attributes at no extra cost\.

+ If you know that some attributes of that table will rarely be used in queries, then there is no reason to project those attributes\. If you subsequently update an attribute that is not in the index, you won't incur the extra cost of updating the index\. You can still retrieve non\-projected attributes in a Query, but at a higher provisioned throughput cost\.

Specify `ALL` only if you want your queries to return the entire table item but you want to sort the table by a different sort key\. Indexing all attributes will eliminate the need for table fetches, but in most cases it will double your costs for storage and write activity\. 

## Optimize Frequent Queries To Avoid Fetches<a name="GuidelinesForLSI.TableFetches"></a>

To get the fastest queries with the lowest possible latency, project all of the attributes that you expect those queries to return\. If you query an index, but the attributes that you request are not projected, DynamoDB will fetch the requested attributes from the table\. To do so, DynamoDB must read the entire item from the table, which introduces latency and additional I/O operations\.

If you only issue certain queries only occasionally, and you don't see the need to project all the requested attributes, keep in mind that these "occasional" queries can often turn into "essential" queries\! You might regret not projecting those attributes after all\.

For more information about table fetches, see [Provisioned Throughput Considerations for Local Secondary Indexes](LSI.md#LSI.ThroughputConsiderations)\.

## Take Advantage of Sparse Indexes<a name="GuidelinesForLSI.SparseIndexes"></a>

For any item in a table, DynamoDB will only write a corresponding index entry if the index sort key value is present in the item\. If the sort key does not appear in every table item, the index is said to be *sparse*\.

Sparse indexes can be beneficial for queries on attributes that don't appear in most table items\. For example, suppose that you have a *CustomerOrders* table that stores all of your orders\. The key attributes for the table would be as follows:

+ Partition key: CustomerId

+ Sort key: OrderId

If you want to track only orders that are open, you can have an attribute named IsOpen\. If you are waiting to receive an order, your application can define IsOpen by writing an "X" \(or any other value\) for that particular item in the table\. When the order arrives, your application can delete the IsOpen attribute to signify that the order has been fulfilled\.

To track open orders, you can create an index on CustomerId \(partition key\) and IsOpen \(sort key\)\. Only those orders in the table with IsOpen defined will appear in the index\. Your application can then quickly and efficiently find the orders that are still open by querying the index\. If you had thousands of orders, for example, but only a small number that are open, the application can query the index and return the OrderId of each open order\. Your application will perform significantly fewer reads than it would take to scan the entire *CustomerOrders* table\.

Instead of writing an arbitrary value into the IsOpen attribute, you can use a different attribute that will result in a useful sort order in the index\. To do this, you can create an OrderOpenDate attribute and set it to the date on which the order was placed \(and still delete the attribute once the order is fulfilled\), and create the OpenOrders index with the schema CustomerId \(partition key\) and OrderOpenDate \(sort key\)\. This way when you query your index, the items will be returned in a more useful sort order\.

## Watch For Expanding Item Collections<a name="GuidelinesForLSI.ItemCollections"></a>

An item collection is all the items in a table and its indexes that have the same partition key\. An item collection cannot exceed 10 GB, so it's possible to run out of space for a particular partition key value\.

When you add or update a table item, DynamoDB will update any local secondary indexes that are affected\. If the indexed attributes are defined in the table, the indexes will grow with the table\.

When you create an index, think about how much data will be written to the index, and how much of that data will have the same partition key value\. If you expect that the sum of table and index items for a particular partition key value will exceed 10 GB, you should consider whether you could avoid creating the index\.

If you cannot avoid creating the index, then you will need to anticipate the item collection size limit and take action before you exceed it\. For strategies on working within the limit and taking corrective action, see [Item Collection Size Limit](LSI.md#LSI.ItemCollections.SizeLimit)\.