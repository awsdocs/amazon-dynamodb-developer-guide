# Throughput Settings for Reads and Writes<a name="ProvisionedThroughput"></a>

**Topics**
+ [Read Capacity Units](#ProvisionedThroughput.CapacityUnits.Read)
+ [Write Capacity Units](#ProvisionedThroughput.CapacityUnits.Write)
+ [Request Throttling and Burst Capacity](#ProvisionedThroughput.Throttling)
+ [Choosing Initial Throughput Settings](#ProvisionedThroughput.CapacityUnits.InitialSettings)
+ [Modifying Throughput Settings](#ProvisionedThroughput.CapacityUnits.Modifying)

When you create a new table in DynamoDB, you must specify its *provisioned throughput capacity*—the amount of read and write activity that the table will be able to support\. DynamoDB uses this information to reserve sufficient system resources to meet your throughput requirements\.

**Note**  
You can optionally allow DynamoDB auto scaling to manage your table's throughput capacity; however, you still must provide initial settings for read and write capacity when you create the table\. DynamoDB auto scaling uses these initial settings as a starting point, and then adjusts them dynamically in response to your application's requirements\.  
For more information, see [Managing Throughput Capacity Automatically with DynamoDB Auto Scaling](AutoScaling.md)\.

DynamoDB automatically distributes your data across partitions, which are stored on multiple servers in the AWS Cloud\. \(For more information, see [Partitions and Data Distribution](HowItWorks.Partitions.md)\.\) For optimal throughput, you should distribute read requests as evenly as possible across these partitions\. For example, suppose that you provision a table with 10,000 read capacity units\. If you issue 10,000 read requests for a single item in the table, all of the read activity will be concentrated on a single partition\. However, if you spread your requests across all items in the table, DynamoDB can access the partitions in parallel\.

As your application data and access requirements change, you might need to adjust your table's throughput settings\. If you're using DynamoDB auto scaling, the throughput settings are automatically adjusted in response to actual workloads\. You can also use the `UpdateTable` operation to manually adjust your table's throughput capacity\. You might decide to do this if you need to bulk\-load data from an existing data store into your new DynamoDB table\. You could create the table with a large write throughput setting and then reduce this setting after the bulk data load is complete\.

You specify throughput requirements in terms of *capacity units*—the amount of data your application needs to read or write per second\. You can modify these settings later, if needed, or enable DynamoDB auto scaling to modify them automatically\.

## Read Capacity Units<a name="ProvisionedThroughput.CapacityUnits.Read"></a>

A *read capacity unit* represents one strongly consistent read per second, or two eventually consistent reads per second, for an item up to 4 KB in size\.

**Note**  
 To learn more about DynamoDB read consistency models, see [Read Consistency](HowItWorks.ReadConsistency.md)\.

For example, suppose that you create a table with 10 provisioned read capacity units\. This will allow you to perform 10 strongly consistent reads per second, or 20 eventually consistent reads per second, for items up to 4 KB\.

Reading an item larger than 4 KB consumes more read capacity units\. For example a strongly consistent read of an item that is 8 KB \(4 KB × 2\) consumes 2 read capacity units\. An eventually consistent read on that same item consumes only 1 read capacity unit\.

Item sizes for reads are rounded up to the next 4 KB multiple\. For example, reading a 3,500\-byte item will consume the same throughput as reading a 4 KB item\.

## Write Capacity Units<a name="ProvisionedThroughput.CapacityUnits.Write"></a>

A *write capacity unit* represents one write per second, for an item up to 1 KB in size\.

For example, suppose that you create a table with 10 write capacity units\. This will allow you to perform 10 writes per second, for items up to 1 KB size per second\. 

Item sizes for writes are rounded up to the next 1 KB multiple\. For example, writing a 500\-byte item will consume the same throughput as writing a 1 KB item\. 

## Request Throttling and Burst Capacity<a name="ProvisionedThroughput.Throttling"></a>

If your application performs reads or writes at a higher rate than your table can support, DynamoDB will begin to *throttle* those requests\. When DynamoDB throttles a read or write, it returns a `ProvisionedThroughputExceededException` to the caller\. The application can then take appropriate action, such as waiting for a short interval before retrying the request\.

**Note**  
We recommend that you use the AWS SDKs for software development\. The AWS SDKs provide built\-in support for retrying throttled requests; you do not need to write this logic yourself\. For more information, see [Error Retries and Exponential Backoff](Programming.Errors.md#Programming.Errors.RetryAndBackoff)\.

The DynamoDB console displays Amazon CloudWatch metrics for your tables, so you can monitor throttled read requests and write requests\. If you encounter excessive throttling, you should consider increasing your table's provisioned throughput settings\.

In some cases, DynamoDB will use *burst capacity* to accommodate reads or writes in excess of your table's throughput settings\. With burst capacity, unexpected read or write requests can succeed where they otherwise would be throttled\. Burst capacity is available on a best\-effort basis, and DynamoDB does not guarantee that this capacity is always available\. For more information, see [Using Burst Capacity Effectively](bp-partition-key-design.md#bp-partition-key-throughput-bursting)\.

## Choosing Initial Throughput Settings<a name="ProvisionedThroughput.CapacityUnits.InitialSettings"></a>

Every application has different requirements for reading and writing from a database\. When you are determining the initial throughput settings for a DynamoDB table, take the following inputs into consideration:
+ **Item sizes\.** Some items are small enough that they can be read or written using a single capacity unit\. Larger items will require multiple capacity units\. By estimating the sizes of the items that will be in your table, you can specify accurate settings for your table's provisioned throughput\.
+ **Expected read and write request rates\.** In addition to item size, you should estimate the number of reads and writes you need to perform per second\.
+ **Read consistency requirements\.** Read capacity units are based on strongly consistent read operations, which consume twice as many database resources as eventually consistent reads\. You should determine whether your application requires strongly consistent reads, or whether it can relax this requirement and perform eventually consistent reads instead\. \(Read operations in DynamoDB are eventually consistent, by default; you can request strongly consistent reads for these operations if necessary\.\)

**Note**  
For recommendations on provisioned throughput and related topics, see [Best Practices for Designing and Using Partition Keys Effectively](bp-partition-key-design.md)\.

## Modifying Throughput Settings<a name="ProvisionedThroughput.CapacityUnits.Modifying"></a>

If you have enabled DynamoDB auto scaling for a table, then its throughput capacity will be dynamically adjusted in response to actual usage\. No manual intervention is required\.

You can modify your table's provisioned throughput settings using the AWS Management Console or the `UpdateTable` operation\. For more information on throughput increases and decreases per day, see [Limits in DynamoDB](Limits.md)\.