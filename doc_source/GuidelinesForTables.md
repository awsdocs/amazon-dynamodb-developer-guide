# Best Practices for Tables<a name="GuidelinesForTables"></a>


+ [Design For Uniform Data Access Across Items In Your Tables](#GuidelinesForTables.UniformWorkload)
+ [Understand Partition Behavior](#GuidelinesForTables.Partitions)
+ [Use Burst Capacity Sparingly](#GuidelinesForTables.Bursting)
+ [Distribute Write Activity During Data Upload](#GuidelinesForTables.DataUpload)
+ [Understand Access Patterns for Time Series Data](#GuidelinesForTables.TimeSeriesDataAccessPatterns)
+ [Cache Popular Items](#GuidelinesForTables.CachePopularItems)
+ [Consider Workload Uniformity When Adjusting Provisioned Throughput](#GuidelinesForTables.AvoidExcessivePTIncreases)
+ [Test Your Application At Scale](#GuidelinesForTables.TestYourAppAtScale)

This section covers some best practices for working with tables\.

## Design For Uniform Data Access Across Items In Your Tables<a name="GuidelinesForTables.UniformWorkload"></a>

The optimal usage of a table's provisioned throughput depends on these factors:

+ The primary key selection\.

+ The workload patterns on individual items\.

The primary key uniquely identifies each item in a table\. The primary key can be simple \(partition key\) or composite \(partition key and sort key\)\.

When it stores data, DynamoDB divides a table's items into multiple partitions and distributes the data primarily based upon the partition key value\. Consequently, to achieve the full throughput you have provisioned for a table, you should keep your workload spread evenly across the whole range of partition key values\. Distributing requests across partition key values also distributes the requests across partitions\.

For example, if you access a small number of partition key values (or even a single value), traffic will likewise concentrate on a few or even one partition\. Because each partition provides a portion of your table's overall throughput, a workload that focuses on one or a few partitions will not use all of your table's provisioned throughput\. To get the most out of provisioned throughput, create tables where the partition key has a large number of distinct values that are requested fairly uniformly, as randomly as possible\. 

This does not mean that you must access all or even a high percentage of the partition key values to achieve your throughput level\. However, when your workload accesses more distinct partition key values, its requests will spread across more partitions, better utilizing your allocated throughput\. In general, you will achieve higher throughput as the ratio of partition key values accessed to the total number of partition key values in a table increases\.

### Choosing a Partition Key<a name="w3ab1c29c15b7c14"></a>

The following table compares some common partition key schemas for provisioned throughput efficiency:


****  

| Partition key value | Uniformity | 
| --- | --- | 
|  User ID, where the application has many users\.  |  Good  | 
| Status code, where there are only a few possible status codes\. | Bad | 
| Item creation date, rounded to the nearest time period \(e\.g\. day, hour, minute\) | Bad | 
| Device ID, where each device accesses data at relatively similar intervals | Good | 
| Device ID, where even if there are a lot of devices being tracked, one is by far more popular than all the others\. | Bad  | 

If a single table has only a very small number of partition key values, consider distributing your write operations across more distinct partition key values\. In other words, structure the primary key elements to avoid one "hot" \(heavily requested\) partition key value that slows overall performance\. 

For example, consider a table with a composite primary key\. The partition key represents the item's creation date, rounded to the nearest day\. The sort key is an item identifier\. On a given day, say `2014-07-09`, all of the new items will be written to that same partition key value\. 

If the table will fit entirely into a single partition \(taking into consideration growth of your data over time\), and if your application's read and write throughput requirements do not exceed the read and write capabilities of a single partition, then your application should not encounter any unexpected throttling as a result of partitioning\. 

However, if you anticipate scaling beyond a single partition, then you should architect your application so that it can use more of the table's full provisioned throughput\. 

#### Randomizing Across Multiple Partition Key Values<a name="GuidelinesForTables.UniformWorkload.RandomizedPartitionKey"></a>

One way to increase the write throughput of this application would be to randomize the writes across multiple partition key values\. Choose a random number from a fixed set \(for example, `1` to `200`\) and concatenate it as a suffix to the date\. This will yield partition key values such as `2014-07-09.1`, `2014-07-09.2` and so on through `2014-07-09.200`\. Because you are randomizing the partition key, the writes to the table on each day are spread evenly across all of the partition key values; this will yield better parallelism and higher overall throughput\.

To read all of the items for a given day, you would need to obtain all of the items for each suffix\. For example, you would first issue a `Query` request for the partition key value `2014-07-09.1`, then another `Query` for `2014-07-09.2`, and so on through `2014-07-09.200`\. Finally, your application would need to merge the results from all of the `Query` requests\.

#### Using a Calculated Value<a name="GuidelinesForTables.UniformWorkload.CalculatedPartitionKey"></a>

A randomizing strategy can greatly improve write throughput; however, it is difficult to read a specific item because you don't know which suffix value was used when writing the item\. To make it easier to read individual items, you can use a different strategy: Instead of using a random number to distribute the items among partitions, use a number that you are able to calculate based upon something that's intrinsic to the item\.

Continuing with our example, suppose that each item has an `OrderId`\. Before your application writes the item to the table, it can calculate a partition key suffix based upon the order ID\. The calculation should result in a number between 1 and 200 that is fairly evenly distributed given any set of names \(or user IDs\.\) 

A simple calculation would suffice, such as the product of the UTF\-8 code point values for the characters in the order ID, modulo 200 \+ 1\. The partition key value would then be the date concatenated with the calculation result as a suffix\. With this strategy, the writes are spread evenly across the partition key values, and thus across the partitions\. You can easily perform a `GetItem` operation on a particular item, because you can calculate the partition key value you need when you want to retrieve a specific `OrderId` value\. 

To read all of the items for a given day, you would still need to `Query` each of the `2014-07-09.N` keys \(where `N` is 1 to 200\), and your application would need to merge all of the results\. However, you will avoid having a single "hot" partition key value taking all of the workload\. 

## Understand Partition Behavior<a name="GuidelinesForTables.Partitions"></a>

DynamoDB manages table partitioning for you automatically, adding new partitions if necessary and distributing provisioned throughput capacity evenly across them\.

You can estimate the number of partitions that DynamoDB will initially allocate for your table, and compare that estimate against your scale and access patterns\. You can also estimate the number of additional partitions that DynamoDB will allocate in response to increased storage or provisioned throughput requirements\. These estimates can help you determine the best table design for your application needs\.

**Note**  
The following details about partition sizes and throughput are subject to change\.

### Initial Allocation of Partitions<a name="GuidelinesForTables.Partitions.Initial"></a>

When you create a new table, DynamoDB allocates the table's partitions according to the provisioned throughput settings that you specify\.

A single partition can support a maximum of 3,000 read capacity units or 1,000 write capacity units\. When you create a new table, the initial number of partitions can be expressed as follows:

```
( readCapacityUnits / 3,000 ) + ( writeCapacityUnits / 1,000 ) = initialPartitions (rounded up)
```

For example, suppose that you created a table with 1,000 read capacity units and 500 write capacity units\. In this case, the initial number of partitions would be:

```
( 1,000 / 3,000 ) + ( 500 / 1,000 ) = 0.8333 --> 1
```

Therefore, a single partition could accommodate all of the table's provisioned throughput requirements\.

However, if you had created the table with 1,000 read capacity units and 1,000 write capacity units, then a single partition would not be able to support the specified throughput capacity:

```
( 1,000 / 3,000 ) + ( 1,000 / 1,000 ) = 1.333 --> 2
```

In this case, the table would require two partitions, each with 500 read capacity units and 500 write capacity units\.

### Subsequent Allocation of Partitions<a name="GuidelinesForTables.Partitions.Scaling"></a>

A single partition can hold approximately 10 GB of data, and can support a maximum of 3,000 read capacity units or 1,000 write capacity units\.

If necessary, DynamoDB can allocate additional partitions to your table by *splitting* an existing partition\. Suppose that one of a table's partitions \(**P**\) exceeded its limit for storage \(10 GB\)\. In this case, DynamoDB would split the partition as follows:

1. Allocate two new partitions \(**P1** and **P2**\)\.

1. Distribute the data from **P** evenly across **P1** and **P2**\.

1. Deallocate **P** from the table\.

The following diagram shows how DynamoDB performs a partition split\. The large squares represent partitions, and the small squares represent data items in the table\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

During a partition split, DynamoDB evenly distributes the data from the old partition to the two new partitions \(the data in other partitions are not affected\)\. The old partition's provisioned throughput capacity is then evenly distributed across the two new partitions \(see [Throughput Capacity Per Partition](#GuidelinesForTables.Partitions.Throughput)\)\.

Note that DynamoDB performs partition splits automatically, in the background\. The table remains fully available for read and write activity at your specified throughput levels\.

A partition split can occur in response to:

+ Increased provisioned throughput settings

+ Increased storage requirements

#### Increased Provisioned Throughput Settings<a name="GuidelinesForTables.Partitions.Scaling.Throughput"></a>

If you increase a table's provisioned throughput and the table's current partitioning scheme cannot accommodate your new requirements, DynamoDB will *double* the current number of partitions\.

For example, suppose that you created a new table with 5,000 read capacity units and 2,000 write capacity units\. Using the information from [Initial Allocation of Partitions](#GuidelinesForTables.Partitions.Initial), you can determine that this new table will require four partitions:

```
( 5,000 / 3,000 ) + ( 2,000 / 1,000 ) = 3.6667 --> 4
```

Each of the four partitions can accommodate 1,250 reads per second \(5,000 read capacity units / 4 partitions\) and 500 writes per second \(2,000 write capacity units / 4 partitions\)\.

Now suppose that you increased the table's read capacity units from 5,000 to 8,000\. The existing four partitions would not be able to support this requirement\. In response \(see [Subsequent Allocation of Partitions](#GuidelinesForTables.Partitions.Scaling)\), DynamoDB would double the number of partitions to eight \(4 \* 2 = 8\)\. Each of the resulting partitions would be able to accommodate 1,000 reads per second \(8,000 read capacity units / 8 partitions\) and 250 writes per second \(2,000 write capacity units / 8 partitions\)\.

The following diagram shows the original four partitions in the table, and the resulting partition scheme after DynamoDB doubles the number of partitions\. The large squares represent partitions, and the small squares represent data items in the table\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

#### Increased Storage Requirements<a name="GuidelinesForTables.Partitions.Scaling.Storage"></a>

If an existing partition fills up with data, DynamoDB will split that partition\. The result will be two partitions, with the data from the old partition divided equally between the new partitions\.

The table described in [Increased Provisioned Throughput Settings](#GuidelinesForTables.Partitions.Scaling.Throughput) has eight partitions, so its maximum capacity would be approximately 80 GB, as shown following:

```
8 partitions * 10 GB = 80 GB
```

If one of these partitions were to fill to capacity, DynamoDB would respond by splitting that partition, for a total of nine partitions and an overall capacity of 90 GB, as shown following:

```
9 partitions * 10 GB = 90 GB
```

The following diagram shows one of the original partitions filling to capacity, and the resulting partition scheme after DynamoDB splits that partition\. The large squares represent partitions, and the small squares represent data items in the table\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

### Throughput Capacity Per Partition<a name="GuidelinesForTables.Partitions.Throughput"></a>

If you estimate the number of partitions in your table, you can determine the approximate throughput capacity per partition\. Suppose that you wanted to create a table with 5,000 read capacity units and 2,000 write capacity units\. DynamoDB would allocate four partitions for the new table:

```
( 5,000 / 3,000 ) + ( 2,000 / 1,000 ) = 3.6667 --> 4
```

You could determine the amount of read and write capacity per partition as follows:

```
5,000 read capacity units / 4 partitions = 1,250 read capacity units per partition

2,000 write capacity units / 4 partitions = 500 write capacity units per partition
```

Now suppose that one of these four partitions were to fill to capacity\. DynamoDB would split that partition, resulting in five partitions allocated to the table\. The read and write capacity per partition would then be distributed as follows: 

```
1,250 read capacity units / 2 partitions = 625 read capacity units per child partition

500 write capacity units / 2 partitions = 250 write capacity units per child partition
```

As a result:

+ Three of the five partitions would each have 1,250 read capacity units and 500 write capacity units\.

+ The other two partitions would each have 625 read capacity units and 250 write capacity units\.

Note that as the number of partitions in a table increases, each partition has fewer read and write capacity units available to it\. \(However, the total provisioned throughput for the table remains the same\.\)

## Use Burst Capacity Sparingly<a name="GuidelinesForTables.Bursting"></a>

DynamoDB provides some flexibility in the per\-partition throughput provisioning\. When you are not fully utilizing a partition's throughput, DynamoDB retains a portion of your unused capacity for later *bursts* of throughput usage\.  DynamoDB currently retains up to five minutes \(300 seconds\) of unused read and write capacity\.  During an occasional burst of read or write activity, these extra capacity units can be consumed very quickly—even faster than the per\-second provisioned throughput capacity that you've defined for your table\. However, do not design your application so that it depends on burst capacity being available at all times: DynamoDB can and does use burst capacity for background maintenance and other tasks without prior notice\.

**Note**  
In the future, these details of burst capacity may change\.

## Distribute Write Activity During Data Upload<a name="GuidelinesForTables.DataUpload"></a>

There are times when you load data from other data sources into DynamoDB\. Typically, DynamoDB partitions your table data on multiple servers\. When uploading data to a table, you get better performance if you upload data to all the allocated servers simultaneously\. For example, suppose you want to upload user messages to a DynamoDB table\. You might design a table that uses a composite primary key in which UserID is the partition key and the MessageID is the sort key\. When uploading data from your source, you might tend to read all message items for a specific user and upload these items to DynamoDB as shown in the sequence in the following table\.


****  

| UserID | MessageID | 
| --- | --- | 
|  U1  |  1  | 
| U1 | 2 | 
| U1 | \.\.\. | 
| U1 | \.\.\. up to 100 | 
|  U2  |  1  | 
| U2 | 2 | 
| U2 | \.\.\. | 
| U2 | \.\.\. up to 200 | 

The problem in this case is that you are not distributing your write requests to DynamoDB across your partition key values\. You are taking one partition key value at a time and uploading all its items, before going to the next partition key value and doing the same\. Behind the scenes, DynamoDB is partitioning the data in your tables across multiple servers\. To fully utilize all of the throughput capacity that has been provisioned for your tables, you need to distribute your workload across your partition key values\. In this case, by directing an uneven amount of upload work toward items all with the same partition key value, you may not be able to fully utilize all of the resources DynamoDB has provisioned for your table\. You can distribute your upload work by uploading one item from each partition key value first\. Then you repeat the pattern for the next set of sort key values for all the items until you upload all the data as shown in the example upload sequence in the following table: 


****  

| UserID | MessageID | 
| --- | --- | 
|  U1  |  1  | 
| U2 | 1 | 
| U3 | 1 | 
| \.\.\. | \.\.\.\. | 
|  U1  |  2  | 
| U2 | 2 | 
| U3 | 2 | 
| \.\.\. | \.\.\. | 

Every upload in this sequence uses a different partition key value, keeping more DynamoDB servers busy simultaneously and improving your throughput performance\. 

## Understand Access Patterns for Time Series Data<a name="GuidelinesForTables.TimeSeriesDataAccessPatterns"></a>

For each table that you create, you specify the throughput requirements\. DynamoDB allocates resources to handle your throughput requirements with sustained low latency\. When you design your application and tables, you should consider your application's access pattern to make the most efficient use of your table's resources\. 

Suppose you design a table to track customer behavior on your site, such as URLs that they click\. You might design the table with a composite primary key consisting of Customer ID as the partition key and date/time as the sort key\. In this application, customer data grows indefinitely over time; however, the applications might show uneven access pattern across all the items in the table where the latest customer data is more relevant and your application might access the latest items more frequently and as time passes these items are less accessed, eventually the older items are rarely accessed\. If this is a known access pattern, you could take it into consideration when designing your table schema\. Instead of storing all items in a single table, you could use multiple tables to store these items\. For example, you could create tables to store monthly or weekly data\. For the table storing data from the latest month or week, where data access rate is high, request higher throughput and for tables storing older data, you could dial down the throughput and save on resources\. 

You can save on resources by storing "hot" items in one table with higher throughput settings, and "cold" items in another table with lower throughput settings\. You can remove old items by simply deleting the tables\. You can optionally backup these tables to other storage options such as Amazon Simple Storage Service \(Amazon S3\)\. Deleting an entire table is significantly more efficient than removing items one\-by\-one, which essentially doubles the write throughput as you do as many delete operations as put operations\.

## Cache Popular Items<a name="GuidelinesForTables.CachePopularItems"></a>

Some items in a table might be more popular than others\. For example, consider the *ProductCatalog* table that is described in [Creating Tables and Loading Sample Data](SampleData.md), and suppose that this table contains millions of different products\. Some products might be very popular among customers, so those items would be consistently accessed more frequently than the others\. As a result, the distribution of read activity on *ProductCatalog* would be highly skewed toward those popular items\.

One solution would be to cache these reads at the application layer\. Caching is a technique that is used in many high\-throughput applications, offloading read activity on hot items to the cache rather than to the database\. Your application can cache the most popular items in memory, or use a product such as [ElastiCache](https://aws.amazon.com/elasticache) to do the same\. 

Continuing with the *ProductCatalog* example, when a customer requests an item from that table, the application would first consult the cache to see if there is a copy of the item there\. If so, it is a cache hit; otherwise, it is a cache miss\. When there is a cache miss, the application would need to read the item from DynamoDB and store a copy of the item in the cache\. Over time, the cache misses would decrease as the cache fills with the most popular items; applications would not need to access DynamoDB at all for these items\.

A caching solution can mitigate the skewed read activity for popular items\. In addition, since it reduces the amount of read activity against the table, caching can help reduce your overall costs for using DynamoDB\. 

## Consider Workload Uniformity When Adjusting Provisioned Throughput<a name="GuidelinesForTables.AvoidExcessivePTIncreases"></a>

As the amount of data in your table grows, or as you provision additional read and write capacity, DynamoDB automatically spreads the data across multiple partitions\. If your application doesn't require as much throughput, you simply decrease it using the `UpdateTable` operation, and pay only for the throughput that you have provisioned\. 

For applications that are designed for use with uniform workloads, DynamoDB's partition allocation activity is not noticeable\. A temporary non\-uniformity in a workload can generally be absorbed by the bursting allowance, as described in [Use Burst Capacity Sparingly](#GuidelinesForTables.Bursting)\. However, if your application must accommodate non\-uniform workloads on a regular basis, you should design your table with DynamoDB's partitioning behavior in mind \(see [Understand Partition Behavior](#GuidelinesForTables.Partitions)\), and be mindful when increasing and decreasing provisioned throughput on that table\.

If you reduce the amount of provisioned throughput for your table, DynamoDB will not decrease the number of partitions\. Suppose that you created a table with a much larger amount of provisioned throughput than your application actually needed, and then decreased the provisioned throughput later\. In this scenario, the provisioned throughput per partition would be less than it would have been if you had initially created the table with less throughput\.

For example, consider a situation where you need to bulk\-load 20 million items into a DynamoDB table\. Assume that each item is 1 KB in size, resulting in 20 GB of data\. This bulk\-loading task will require a total of 20 million write capacity units\. To perform this data load within 30 minutes, you would need to set the provisioned write throughput of the table to 11,000 write capacity units\.

The maximum write throughput of a partition is 1000 write capacity units \(see [Understand Partition Behavior](#GuidelinesForTables.Partitions)\); therefore, DynamoDB will create 11 partitions, each with 1000 provisioned write capacity units\.

After the bulk data load, your steady\-state write throughput requirements might be much lower — for example, suppose that your applications only require 200 writes per second\. If you decrease the table's provisioned throughput to this level, each of the 11 partitions will have around 20 write capacity units per second provisioned\. This level of per\-partition provisioned throughput, combined with DynamoDB's bursting behavior, might be adequate for the application\.

However, if an application will require sustained write throughput above 20 writes per second per partition, you should either: \(a\) design a schema that requires fewer writes per second per partition key value, or \(b\) design the bulk data load so that it runs at a slower pace and reduces the initial throughput requirement\. For example, suppose that it was acceptable to run the bulk import for over 3 hours, instead of just 30 minutes\. In this scenario, only 1900 write capacity units per second needs to be provisioned, rather than 11,000\. As a result, DynamoDB would create only two partitions for the table\.

## Test Your Application At Scale<a name="GuidelinesForTables.TestYourAppAtScale"></a>

Many tables begin with small amounts of data, but then grow larger as applications perform write activity\. This growth can occur gradually, without exceeding the provisioned throughput settings you have defined for the table\. As your table grows larger, DynamoDB automatically scales your table out by distributing the data across more partitions\. When this occurs, the provisioned throughput that is allocated to each resulting partition is less than that which is allocated for the original partition\(s\)\.

Suppose that your application accesses the table's data across all of the partition key values, but in a non\-uniform fashion \(accessing a small number of partition key values more frequently than others\)\. Your application might perform acceptably when there is not very much data in the table\. However, as the table becomes larger, there will be more partitions and less throughput per partition\. You might discover that your application is throttled when it attempts to use the same non\-uniform access pattern that worked in the past\.

To avoid problems with "hot" keys when your table becomes larger, make sure that you test your application design at scale\. Consider the ratio of storage to throughput when running at scale, and how DynamoDB will allocate partitions to the table\. \(For more information, see [Understand Partition Behavior](#GuidelinesForTables.Partitions)\.\) 

 If it isn't possible for you to generate a large amount of test data, you can create a table that has very high provisioned throughput settings\. This will create a table with many partitions; you can then use UpdateTable to reduce the settings, but keep the same ratio of storage to throughput that you determined for running the application at scale\. You now have a table that has the throughput\-per\-partition ratio that you expect after it grows to scale\. Test your application against this table using a realistic workload\.

Tables that store time series data can grow in an unbounded manner, and can cause slower application performance over time\. With time series data, applications typically read and write the most recent items in the table more frequently than older items\. If you can remove older time series data from your real\-time table, and archive that data elsewhere, you can maintain a high ratio of throughput per partition\. 

For best practices with time series data, [Understand Access Patterns for Time Series Data](#GuidelinesForTables.TimeSeriesDataAccessPatterns)\. 
