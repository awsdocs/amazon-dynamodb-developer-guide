# Best Practices for Global Secondary Indexes<a name="GuidelinesForGSI"></a>


+ [Choose a Key That Will Provide Uniform Workloads](#GuidelinesForGSI.UniformWorkloads)
+ [Take Advantage of Sparse Indexes](#GuidelinesForGSI.SparseIndexes)
+ [Use a Global Secondary Index For Quick Lookups](#GuidelinesForGSI.QuickLookups)
+ [Create an Eventually Consistent Read Replica](#GuidelinesForGSI.DataMirror)

This section covers some best practices for global secondary indexes\.

## Choose a Key That Will Provide Uniform Workloads<a name="GuidelinesForGSI.UniformWorkloads"></a>

When you create a DynamoDB table, it's important to distribute the read and write activity evenly across the entire table\. To do this, you choose attributes for the primary key so that the data is evenly spread across multiple partitions\.

This same guidance is true for global secondary indexes\. Choose partition keys and sort keys that have a high number of values relative to the number of items in the index\. In addition, remember that global secondary indexes do not enforce uniqueness, so you need to understand the cardinality of your key attributes\. *Cardinality* refers to the distinct number of values in a particular attribute, relative to the number of items that you have\. 

For example, suppose you have an Employee table with attributes such as *Name*, *Title*, *Address*, *PhoneNumber*, *Salary*, and *PayLevel*\. Now suppose that you had a global secondary index named *PayLevelIndex*, with *PayLevel* as the partition key\. Many companies only have a very small number of pay codes, often fewer than ten, even for companies with hundreds of thousands of employees\. Such an index would not provide much benefit, if any, for an application\.

Another problem with *PayLevelIndex* is the uneven distribution of distinct values\. For example, there may be only a few top executives in the company, but a very large number of hourly workers\. Queries on *PayLevelIndex* will not be very efficient because the read activity will not be evenly distributed across partitions\.

## Take Advantage of Sparse Indexes<a name="GuidelinesForGSI.SparseIndexes"></a>

For any item in a table, DynamoDB will only write a corresponding entry to a global secondary index if the index key value is present in the item\. For global secondary indexes, this is the index partition key and its sort key \(if present\)\. If the index key value\(s\) do not appear in every table item, the index is said to be *sparse*\.

You can use a sparse global secondary index to efficiently locate table items that have an uncommon attribute\. To do this, you take advantage of the fact that table items that do not contain global secondary index attribute\(s\) are not indexed at all\. For example, in the *GameScores* table, certain players might have earned a particular achievement for a game \- such as "Champ" \- but most players have not\. Rather than scanning the entire *GameScores* table for Champs, you could create a global secondary index with a partition key of *Champ* and a sort key of *UserId*\. This would make it easy to find all the Champs by querying the index instead of scanning the table\.

Such a query can be very efficient, because the number of items in the index will be significantly fewer than the number of items in the table\. In addition, the fewer table attributes you project into the index, the fewer read capacity units you will consume from the index\.

## Use a Global Secondary Index For Quick Lookups<a name="GuidelinesForGSI.QuickLookups"></a>

You can create a global secondary index using any table attributes for the index partition key and sort key\. You can even create an index that has exactly the same key attributes as that of the table, and project just a subset of non\-key attributes\.

One use case for a global secondary index with a duplicate key schema is for quick lookups of table data, with minimal provisioned throughput\. If the table has a large number of attributes, and those attributes themselves are large, then every query on that table might consume a large amount of read capacity\. If most of your queries do not require that much data to be returned, you can create a global secondary index with a bare minimum of projected attributes \- including no projected attributes at all, other than the table's key\. This lets you query a much smaller global secondary index, and if you really require the additional attributes, you can then query the table using the same key values\.

## Create an Eventually Consistent Read Replica<a name="GuidelinesForGSI.DataMirror"></a>

You can create a global secondary index that has the same key schema as the table, with some \(or all\) of the non\-key attributes projected into the index\. In your application, you can direct some \(or all\) read activity to this index, rather than to the table\. This lets you avoid having to modify the provisioned read capacity on the table, in response to increased read activity\. Note that there will be a short propagation delay between a write to the table and the time that the data appears in the index; your applications should expect eventual consistency when reading from the index\.

You can create multiple global secondary indexes to support your application's characteristics\. For example, suppose that you have two applications with very different read characteristics — a high\-priority app that requires the highest levels of read performance, and a low\-priority app that can tolerate occasional throttling of read activity\. If both of these apps read from the same table, there is a chance that they could interfere with each other: A heavy read load from the low\-priority app could consume all of the available read capacity for the table, which would in turn cause the high\-priority app's read activity to be throttled\. If you create two global secondary indexes — one with a high provisioned read throughput setting, and the other with a lower setting — you can effectively disentangle these two different workloads, with read activity from each application being redirected to its own index\. This approach lets you tailor the amount of provisioned read throughput to each application's read characteristics\.

In some situations, you might want to restrict the applications that can read from the table\. For example, you might have an application that captures clickstream activity from a website, with frequent writes to a DynamoDB table\. You might decide to isolate this table by preventing read access by most applications\. \(For more information, see [Using IAM Policy Conditions for Fine\-Grained Access Control](specifying-conditions.md)\.\) However, if you have other apps that need to perform ad hoc queries on the data, you can create one or more global secondary indexes for that purpose\. When you create the index\(es\), be sure to project only the attributes that your applications actually require\. The apps can then read more data while consuming less provisioned read capacity, instead of having to read large items from the table\. This can result in a significant cost savings over time\.