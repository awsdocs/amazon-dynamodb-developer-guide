# Managing settings on DynamoDB provisioned capacity tables<a name="ProvisionedThroughput"></a>

When you create a new provisioned table in Amazon DynamoDB, you must specify its *provisioned throughput capacity*\. This is the amount of read and write activity that the table can support\. DynamoDB uses this information to reserve sufficient system resources to meet your throughput requirements\.

**Note**  
 You can create an on\-demand mode table instead so that you don't have to manage any capacity settings for servers, storage, or throughput\. DynamoDB instantly accommodates your workloads as they ramp up or down to any previously reached traffic level\. If a workload’s traffic level hits a new peak, DynamoDB adapts rapidly to accommodate the workload\. For more information, see [On\-demand mode](HowItWorks.ReadWriteCapacityMode.md#HowItWorks.OnDemand)\. 

You can optionally allow DynamoDB auto scaling to manage your table's throughput capacity\. However, you still must provide initial settings for read and write capacity when you create the table\. DynamoDB auto scaling uses these initial settings as a starting point, and then adjusts them dynamically in response to your application's requirements\. For more information, see [Managing throughput capacity automatically with DynamoDB auto scaling](AutoScaling.md)\.

As your application data and access requirements change, you might need to adjust your table's throughput settings\. If you're using DynamoDB auto scaling, the throughput settings are automatically adjusted in response to actual workloads\. You can also use the `UpdateTable` operation to manually adjust your table's throughput capacity\. You might decide to do this if you need to bulk\-load data from an existing data store into your new DynamoDB table\. You could create the table with a large write throughput setting and then reduce this setting after the bulk data load is complete\.

You specify throughput requirements in terms of *capacity units*—the amount of data your application needs to read or write per second\. You can modify these settings later, if needed, or enable DynamoDB auto scaling to modify them automatically\.

**Topics**
+ [Read capacity units](#ProvisionedThroughput.CapacityUnits.Read)
+ [Write capacity units](#ProvisionedThroughput.CapacityUnits.Write)
+ [Request throttling and burst capacity](#ProvisionedThroughput.Throttling)
+ [Request throttling and adaptive capacity](#ProvisionedThroughput.AdaptiveCapacity)
+ [Choosing initial throughput settings](#ProvisionedThroughput.CapacityUnits.InitialSettings)
+ [Modifying throughput settings](#ProvisionedThroughput.CapacityUnits.Modifying)
+ [Troubleshooting throttling issues](#ProvisionedThroughput.Troubleshooting)
+ [Using CloudWatch metrics to investigate throttling](#ProvisionedThroughput.CWMetricsForThrottling)

## Read capacity units<a name="ProvisionedThroughput.CapacityUnits.Read"></a>

A *read capacity unit* represents one strongly consistent read per second, or two eventually consistent reads per second, for an item up to 4 KB in size\.

**Note**  
 To learn more about DynamoDB read consistency models, see [Read consistency](HowItWorks.ReadConsistency.md)\.

For example, suppose that you create a table with 10 provisioned read capacity units\. This allows you to perform 10 strongly consistent reads per second, or 20 eventually consistent reads per second, for items up to 4 KB\.

Reading an item larger than 4 KB consumes more read capacity units\. For example, a strongly consistent read of an item that is 8 KB \(4 KB × 2\) consumes 2 read capacity units\. An eventually consistent read on that same item consumes only 1 read capacity unit\.

Item sizes for reads are rounded up to the next 4 KB multiple\. For example, reading a 3,500\-byte item consumes the same throughput as reading a 4 KB item\.

### Capacity unit consumption for reads<a name="ItemSizeCalculations.Reads"></a>

The following describes how DynamoDB read operations consume read capacity units:
+ `GetItem`—Reads a single item from a table\. To determine the number of capacity units that `GetItem` will consume, take the item size and round it up to the next 4 KB boundary\. If you specified a strongly consistent read, this is the number of capacity units required\. For an eventually consistent read \(the default\), divide this number by two\.

  For example, if you read an item that is 3\.5 KB, DynamoDB rounds the item size to 4 KB\. If you read an item of 10 KB, DynamoDB rounds the item size to 12 KB\.
+ `BatchGetItem`—Reads up to 100 items, from one or more tables\. DynamoDB processes each item in the batch as an individual `GetItem` request, so DynamoDB first rounds up the size of each item to the next 4 KB boundary, and then calculates the total size\. The result is not necessarily the same as the total size of all the items\. For example, if `BatchGetItem` reads a 1\.5 KB item and a 6\.5 KB item, DynamoDB calculates the size as 12 KB \(4 KB \+ 8 KB\), not 8 KB \(1\.5 KB \+ 6\.5 KB\)\. 
+ `Query`—Reads multiple items that have the same partition key value\. All items returned are treated as a single read operation, where DynamoDB computes the total size of all items and then rounds up to the next 4 KB boundary\. For example, suppose your query returns 10 items whose combined size is 40\.8 KB\. DynamoDB rounds the item size for the operation to 44 KB\. If a query returns 1500 items of 64 bytes each, the cumulative size is 96 KB\.
+ `Scan`—Reads all items in a table\. DynamoDB considers the size of the items that are evaluated, not the size of the items returned by the scan\.

If you perform a read operation on an item that does not exist, DynamoDB still consumes provisioned read throughput: A strongly consistent read request consumes one read capacity unit, while an eventually consistent read request consumes 0\.5 of a read capacity unit\.

For any operation that returns items, you can request a subset of attributes to retrieve\. However, doing so has no impact on the item size calculations\. In addition, `Query` and `Scan` can return item counts instead of attribute values\. Getting the count of items uses the same quantity of read capacity units and is subject to the same item size calculations\. This is because DynamoDB has to read each item in order to increment the count\.

#### Read operations and read consistency<a name="ItemSizeCalculations.Reads.Consistency"></a>

The preceding calculations assume strongly consistent read requests\. For an eventually consistent read request, the operation consumes only half the capacity units\. For an eventually consistent read, if the total item size is 80 KB, the operation consumes only 10 capacity units\.

## Write capacity units<a name="ProvisionedThroughput.CapacityUnits.Write"></a>

A *write capacity unit* represents one write per second, for an item up to 1 KB in size\.

For example, suppose that you create a table with 10 write capacity units\. This allows you to perform 10 writes per second, for items up to 1 KB in size per second\. 

Item sizes for writes are rounded up to the next 1 KB multiple\. For example, writing a 500\-byte item consumes the same throughput as writing a 1 KB item\. 

### Capacity unit consumption for writes<a name="ItemSizeCalculations.Writes"></a>

The following describes how DynamoDB write operations consume write capacity units:
+ `PutItem`—Writes a single item to a table\. If an item with the same primary key exists in the table, the operation replaces the item\. For calculating provisioned throughput consumption, the item size that matters is the larger of the two\.
+ `UpdateItem`—Modifies a single item in the table\. DynamoDB considers the size of the item as it appears before and after the update\. The provisioned throughput consumed reflects the larger of these item sizes\. Even if you update just a subset of the item's attributes, `UpdateItem` will still consume the full amount of provisioned throughput \(the larger of the "before" and "after" item sizes\)\. 
+ `DeleteItem`—Removes a single item from a table\. The provisioned throughput consumption is based on the size of the deleted item\.
+ `BatchWriteItem`—Writes up to 25 items to one or more tables\. DynamoDB processes each item in the batch as an individual `PutItem` or `DeleteItem` request \(updates are not supported\)\. So DynamoDB first rounds up the size of each item to the next 1 KB boundary, and then calculates the total size\. The result is not necessarily the same as the total size of all the items\. For example, if `BatchWriteItem` writes a 500\-byte item and a 3\.5 KB item, DynamoDB calculates the size as 5 KB \(1 KB \+ 4 KB\), not 4 KB \(500 bytes \+ 3\.5 KB\)\.

For `PutItem`, `UpdateItem`, and `DeleteItem` operations, DynamoDB rounds the item size up to the next 1 KB\. For example, if you put or delete an item of 1\.6 KB, DynamoDB rounds the item size up to 2 KB\.

`PutItem`, `UpdateItem`, and `DeleteItem` allow *conditional writes*, where you specify an expression that must evaluate to true in order for the operation to succeed\. If the expression evaluates to false, DynamoDB still consumes write capacity units from the table: 
+ For an existing item, the number of write capacity units consumed depends on the size of the new item\. \(For example, a failed conditional write of a 1 KB item would consume one write capacity unit\. If the new item were twice that size, the failed conditional write would consume two write capacity units\.\) 
+ For a new item, DynamoDB consumes one write capacity unit\.

## Request throttling and burst capacity<a name="ProvisionedThroughput.Throttling"></a>

If your application performs reads or writes at a higher rate than your table can support, DynamoDB begins to *throttle* those requests\. When DynamoDB throttles a read or write, it returns a `ProvisionedThroughputExceededException` to the caller\. The application can then take appropriate action, such as waiting for a short interval before retrying the request\.

**Note**  
We recommend that you use the AWS SDKs for software development\. The AWS SDKs provide built\-in support for retrying throttled requests; you do not need to write this logic yourself\. For more information, see [Error retries and exponential backoff](Programming.Errors.md#Programming.Errors.RetryAndBackoff)\.

The DynamoDB console displays Amazon CloudWatch metrics for your tables, so you can monitor throttled read requests and write requests\. If you encounter excessive throttling, you should consider increasing your table's provisioned throughput settings\.

In some cases, DynamoDB uses *burst capacity* to accommodate reads or writes in excess of your table's throughput settings\. With burst capacity, unexpected read or write requests can succeed where they otherwise would be throttled\. For more information, see [Using burst capacity effectively](bp-partition-key-design.md#bp-partition-key-throughput-bursting)\.

## Request throttling and adaptive capacity<a name="ProvisionedThroughput.AdaptiveCapacity"></a>

DynamoDB automatically distributes your data across partitions, which are stored on multiple servers in the AWS Cloud \(For more information, see [Partitions and data distribution](HowItWorks.Partitions.md)\)\. It's not always possible to distribute read and write activity evenly all the time\. When data access is imbalanced, a "hot" partition can receive such a higher volume of read and write traffic compared to other partitions\. Adaptive capacity works by automatically increasing throughput capacity for partitions that receive more traffic\. For more information, see [Understanding DynamoDB adaptive capacity](bp-partition-key-design.md#bp-partition-key-partitions-adaptive)\. 

## Choosing initial throughput settings<a name="ProvisionedThroughput.CapacityUnits.InitialSettings"></a>

Every application has different requirements for reading and writing from a database\. When you are determining the initial throughput settings for a DynamoDB table, take the following inputs into consideration:
+ **Item sizes\.** Some items are small enough that they can be read or written using a single capacity unit\. Larger items require multiple capacity units\. By estimating the sizes of the items that will be in your table, you can specify accurate settings for your table's provisioned throughput\.
+ **Expected read and write request rates\.** In addition to item size, you should estimate the number of reads and writes you need to perform per second\.
+ **Read consistency requirements\.** Read capacity units are based on strongly consistent read operations, which consume twice as many database resources as eventually consistent reads\. You should determine whether your application requires strongly consistent reads, or whether it can relax this requirement and perform eventually consistent reads instead\. \(Read operations in DynamoDB are eventually consistent, by default\. You can request strongly consistent reads for these operations if necessary\.\)

For example, suppose that you want to read 80 items per second from a table\. The items are 3 KB in size, and you want strongly consistent reads\. For this scenario, each read requires one provisioned read capacity unit\. To determine this number, you divide the item size of the operation by 4 KB, and then round up to the nearest whole number, as in this example:
+ 3 KB / 4 KB = 0\.75, or **1** read capacity unit

For this scenario, you have to set the table's provisioned read throughput to 80 read capacity units:
+ 1 read capacity unit per item × 80 reads per second = **80** read capacity units 

Now suppose that you want to write 100 items per second to your table, and that the items are 512 bytes in size\. For this scenario, each write requires one provisioned write capacity unit\. To determine this number, you divide the item size of the operation by 1 KB, and then round up to the nearest whole number:
+ 512 bytes / 1 KB = 0\.5, or **1**

For this scenario, you would want to set the table's provisioned write throughput to 100 write capacity units:
+ 1 write capacity unit per item × 100 writes per second = **100** write capacity units

**Note**  
For recommendations on provisioned throughput and related topics, see [Best practices for designing and using partition keys effectively](bp-partition-key-design.md)\.

## Modifying throughput settings<a name="ProvisionedThroughput.CapacityUnits.Modifying"></a>

If you have enabled DynamoDB auto scaling for a table, then its throughput capacity is dynamically adjusted in response to actual usage\. No manual intervention is required\.

You can modify your table's provisioned throughput settings using the AWS Management Console or the `UpdateTable` operation\. For more information about throughput increases and decreases per day, see [Service, account, and table quotas in Amazon DynamoDB](ServiceQuotas.md)\.

## Troubleshooting throttling issues<a name="ProvisionedThroughput.Troubleshooting"></a>

For troubleshooting issues that appear to be related to throttling, an important first is to confirm if the throttling is coming from DynamoDB or from the application\. 

Following are some common scenarios, and possible steps to help resolve them\.

### The DynamoDB table appears to have enough provisioned capacity, but requests are being throttled\.<a name="ProvisionedThroughput.Troubleshooting.Reason1"></a>

This can occur when the throughput is below the average per minute, but throughput exceeds the amount available per second DynamoDB only reports minute level metrics to CloudWatch, which are then calculated as the sum for one minute and averaged\. But DynamoDB itself applies rate limits per second\. So if too much of that throughput occurs within a small portion of that minute, such as few seconds or less, then requests for the rest of that minute can be throttled\. For example, if we have provisioned 60 WCU on a table then it can do 3600 writes in 1 minute\. But if all 3600 WCU requests hit in the same second, then the rest of that minute will be throttled\.

 One way to resolve this scenario can be to add some jitters and exponential back off to the API calls\. For more information see this post about [backoff and jitter](https://aws.amazon.com/tr/blogs/architecture/exponential-backoff-and-jitter/)\.

### Autoscaling is enabled, but tables are still being throttled\.<a name="ProvisionedThroughput.Troubleshooting.Reason2"></a>

This can occur during sudden spikes in traffic\. Autoscaling can be triggered when 2 data points breach the configured target utilization value within a 1\-minute span\. Therefore, Autoscaling can take place because the consumed capacity is above target utilization for 2 consistent minutes\. But if the spikes are more than 1 minute apart, Autoscaling might not be triggered\. Similarly, a scale down event can be triggered when 15 consecutive data points are lower than the target utilization\. In either case, after Autoscaling is triggered an `UpdateTable API` call is invoked\. It can then take several minutes to update the Provisioned Capacity for the table or the index\. During this period, any requests that exceed the previous Provisioned Capacity of the tables will be throttled\.

 In summary, Autoscaling requires consecutive data points where the target utilization value is being breached to scale up a DynamoDB table\. For this reason Autoscaling is not recommended as a solution for dealing with spiked workloads\. Please refer to the [Autoscaling documentation](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/CostOptimization_AutoScalingSettings.html) for more information\.

### Tables are in "On\-Demand" capacity mode but are still being throttled\.<a name="ProvisionedThroughput.Troubleshooting.Reason3"></a>

For On\-Demand tables, DynamoDB automatically allocates more capacity as your traffic volume increases\. As long as the access is distributed evenly across partitions, and the table does not exceed double its previous peak traffic, the overall table will not be throttled\. However, throttling can still occur if the throughput exceeds double the previous peak within the same 30 minutes\. Please refer to [OnDemand scaling](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.ReadWriteCapacityMode.html#HowItWorks.PeakTraffic) for further information\.

### A hot key may be causing throttling issues\.<a name="ProvisionedThroughput.Troubleshooting.Reason4"></a>

In DynamoDB, a partition key that does not have high cardinality can result in many requests which target just a few partitions\. If a resulting "hot partition" goes past the per partition limits of 3000 RCU or 1000 WCU per second, this can result in throttling\. The diagnostic tool CloudWatch Contributor Insights, can help debug this, by providing CCI graphs for each table’s item access patterns\. You can continuously monitor your DynamoDB tables' most frequently accessed keys and other traffic trends\. To find out more about CloudWatch Contributor Insights see [CCI](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/contributorinsights_HowItWorks.html)\. For specific information on choosing the right hot key see [Choosing the right hot key](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/contributorinsights_HowItWorks.html)\.

## Using CloudWatch metrics to investigate throttling<a name="ProvisionedThroughput.CWMetricsForThrottling"></a>

Below are some DynamoDB metrics to monitor during throttling events, to help locate which operations are creating throttled requests and identify root issues\.

1\. `ThrottledRequests`
+ One throttled request can contain multiple throttled events, so events can be more relevant to examine\.

  For example, when you update an item in a table with GSIs there are multiple events \- a write to the table, and a write to each index\. Even if one or more of these events are throttled, there will only be one ThrottledRequest\.

2\. `ReadThrottleEvents`
+ Watch for requests that exceed the provisioned RCU for a table or GSI\.

3\. `WriteThrottleEvents`
+ Watch for requests that exceed the provisioned WCU for a table or GSI\.

4\. `OnlineIndexConsumedWriteCapacity`
+ Pay attention to the number of WCU consumed when adding a new GSI to a table\. Note that `ConsumedWriteCapacityUnits` for a GSI does not include the WCU consumed during index creation\.
+  If you have set the WCU for a GSI too low, then incoming write activity during the backfill phase might be throttled\.

5\. `OnlineIndexThrottleEvents`
+ Review the number of write throttle events that occur when adding a new GSI to a table\.
+ If you find that the WCU is set too low and is being throttled, you can update the WCU value for a GSI even during backfill\.

6\. `Provisioned Read/Write`
+ View how many provisioned read or write capacity units were consumed over the specified time period, for a table or a specified global secondary index\.
+ Note that the `TableName` dimension returns `ProvisionedReadCapacityUnits` for the table only by default\. To view the numer of provisioned read or write capacity units for a global secondary index, you must specify both `TableName` and `GlobalSecondaryIndexName`\.

7\. `Consumed Read/Write`
+ View how many read or write capacity units were consumed over the specified time period\. 

For more information on DynamoDB CloudWatch Metrics, see [Metrics and Dimensions](metrics-dimensions.md)\.