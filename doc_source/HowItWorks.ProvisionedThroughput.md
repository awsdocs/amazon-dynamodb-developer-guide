# Throughput Capacity for Reads and Writes<a name="HowItWorks.ProvisionedThroughput"></a>


+ [DynamoDB Auto Scaling](#HowItWorks.ProvisionedThroughput.AutoScaling)
+ [Provisioned Throughput](#HowItWorks.ProvisionedThroughput.Manual)
+ [Reserved Capacity](#HowItWorks.ProvisionedThroughput.ReservedCapacity)

When you create a table or index in Amazon DynamoDB, you must specify your capacity requirements for read and write activity\. By defining your throughput capacity in advance, DynamoDB can reserve the necessary resources to meet the read and write activity your application requires, while ensuring consistent, low\-latency performance\.

You specify throughput capacity in terms of read capacity units and write capacity units:

+ One *read capacity unit* represents one strongly consistent read per second, or two eventually consistent reads per second, for an item up to 4 KB in size\. If you need to read an item that is larger than 4 KB, DynamoDB will need to consume additional read capacity units\. The total number of read capacity units required depends on the item size, and whether you want an eventually consistent or strongly consistent read\.

+ One *write capacity unit* represents one write per second for an item up to 1 KB in size\. If you need to write an item that is larger than 1 KB, DynamoDB will need to consume additional write capacity units\. The total number of write capacity units required depends on the item size\.

For example, suppose that you create a table with 5 read capacity units and 5 write capacity units\. With these settings, your application could:

+ Perform strongly consistent reads of up to 20 KB per second \(4 KB × 5 read capacity units\)\.

+ Perform eventually consistent reads of up to 40 KB per second \(twice as much read throughput\)\.

+ Write up to 5 KB per second \(1 KB × 5 write capacity units\)\.

If your application reads or writes larger items \(up to the DynamoDB maximum item size of 400 KB\), it will consume more capacity units\.

If your read or write requests exceed the throughput settings for a table, DynamoDB can *throttle* that request\. DynamoDB can also throttle read requests exceeds for an index\. Throttling prevents your application from consuming too many capacity units\. When a request is throttled, it fails with an HTTP 400 code \(`Bad Request`\) and a `ProvisionedThroughputExceededException`\. The AWS SDKs have built\-in support for retrying throttled requests \(see [Error Retries and Exponential Backoff](Programming.Errors.md#Programming.Errors.RetryAndBackoff)\), so you do not need to write this logic yourself\.

You can use the AWS Management Console to monitor your provisioned and actual throughput, and to modify your throughput settings if necessary\.

DynamoDB provides the following mechanisms for managing throughput:

+ DynamoDB auto scaling

+ Provisioned throughput

+ Reserved capacity

## DynamoDB Auto Scaling<a name="HowItWorks.ProvisionedThroughput.AutoScaling"></a>

DynamoDB auto scaling actively manages throughput capacity for tables and global secondary indexes\. With auto scaling, you define a range \(upper and lower limits\) for read and write capacity units\. You also define a target utilization percentage within that range\. DynamoDB auto scaling seeks to maintain your target utilization, even as your application workload increases or decreases\.

With DynamoDB auto scaling, a table or a global secondary index can increase its provisioned read and write capacity to handle sudden increases in traffic, without request throttling\. When the workload decreases, DynamoDB auto scaling can decrease the throughput so that you don't pay for unused provisioned capacity\.

**Note**  
If you use the AWS Management Console to create a table or a global secondary index, DynamoDB auto scaling is enabled by default\.  
You can manage auto scaling settings at any time by using the console, the AWS CLI, or one of the AWS SDKs\.

For more information, see [Managing Throughput Capacity Automatically with DynamoDB Auto Scaling](AutoScaling.md)\.

## Provisioned Throughput<a name="HowItWorks.ProvisionedThroughput.Manual"></a>

If you aren't using DynamoDB auto scaling, you have to manually define your throughput requirements\. *Provisioned throughput* is the maximum amount of capacity that an application can consume from a table or index\. If your application exceeds your provisioned throughput settings, it is subject to request throttling\.

For example, suppose that you want to read 80 items per second from a table\. The items are 3 KB in size, and you want strongly consistent reads\. For this scenario, each read requires one provisioned read capacity unit\. To determine this, you divide the item size of the operation by 4 KB, and then round up to the nearest whole number, as in this example:

+ 3 KB / 4 KB = 0\.75, or **1** read capacity unit

For this scenario, you have to set the table's provisioned read throughput to 80 read capacity units:

+ 1 read capacity unit per item × 80 reads per second = **80** read capacity units 

Now suppose that you want to write 100 items per second to your table, and that the items are 512 bytes in size\. For this scenario, each write requires one provisioned write capacity unit\. To determine this, you divide the item size of the operation by 1 KB, and then round up to the nearest whole number:

+ 512 bytes / 1 KB = 0\.5, or **1**

For this scenario, you would want to set the table's provisioned write throughput to 100 write capacity units:

+ 1 write capacity unit per item × 100 writes per second = **100** write capacity units

For more information see [Item Sizes and Capacity Unit Consumption](CapacityUnitCalculations.md)\.

## Reserved Capacity<a name="HowItWorks.ProvisionedThroughput.ReservedCapacity"></a>

As a DynamoDB customer, you can purchase *reserved capacity* in advance, as described at [Amazon DynamoDB Pricing](https://aws.amazon.com/dynamodb/pricing)\. With reserved capacity, you pay a one\-time upfront fee and commit to a minimum usage level over a period of time\. By reserving your read and write capacity units ahead of time, you realize significant cost savings compared to on\-demand provisioned throughput settings\.

To manage reserved capacity, go to the [DynamoDB console](https://console.aws.amazon.com/dynamodb) and choose **Reserved Capacity**\.

**Note**  
You can prevent users from viewing or purchasing reserved capacity, while still allowing them to access the rest of the console\. For more information, see "Grant Permissions to Prevent Purchasing of Reserved Capacity Offerings" in [Authentication and Access Control for Amazon DynamoDB](authentication-and-access-control.md)\.