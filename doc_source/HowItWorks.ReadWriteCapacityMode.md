# Read/write capacity mode<a name="HowItWorks.ReadWriteCapacityMode"></a>

Amazon DynamoDB has two read/write capacity modes for processing reads and writes on your tables: 
+  On\-demand 
+  Provisioned \(default, free\-tier eligible\) 

The read/write capacity mode controls how you are charged for read and write throughput and how you manage capacity\. You can set the read/write capacity mode when creating a table or you can change it later\.

Secondary indexes inherit the read/write capacity mode from the base table\. For more information, see [Considerations when changing read/write Capacity Mode](switching.capacitymode.md)\.

The following video will give you an introductory look at table capacity modes\.

[![AWS Videos](http://img.youtube.com/vi/https://www.youtube.com/embed/DdXK1XYJduw/0.jpg)](http://www.youtube.com/watch?v=https://www.youtube.com/embed/DdXK1XYJduw)

For more information on best practices for optimizing costs of your DynamoDB tables, see [Optimizing costs on DynamoDB tables](bp-cost-optimization.md)\.

**Topics**
+ [On\-demand mode](#HowItWorks.OnDemand)
+ [Provisioned mode](#HowItWorks.ProvisionedThroughput.Manual)

## On\-demand mode<a name="HowItWorks.OnDemand"></a>

Amazon DynamoDB on\-demand is a flexible billing option capable of serving thousands of requests per second without capacity planning\. DynamoDB on\-demand offers pay\-per\-request pricing for read and write requests so that you pay only for what you use\. 

 When you choose on\-demand mode, DynamoDB instantly accommodates your workloads as they ramp up or down to any previously reached traffic level\. If a workload’s traffic level hits a new peak, DynamoDB adapts rapidly to accommodate the workload\. Tables that use on\-demand mode deliver the same single\-digit millisecond latency, service\-level agreement \(SLA\) commitment, and security that DynamoDB already offers\. You can choose on\-demand for both new and existing tables and you can continue using the existing DynamoDB APIs without changing code\.

On\-demand mode is a good option if any of the following are true: 
+ You create new tables with unknown workloads\. 
+ You have unpredictable application traffic\. 
+ You prefer the ease of paying for only what you use\. 

 The request rate is only limited by the DynamoDB throughput default table quotas, but it can be raised upon request\. For more information, see [Throughput default quotas](ServiceQuotas.md#default-limits-throughput)\. 

 To get started with on\-demand, you can create or update a table to use on\-demand mode\. For more information, see [Basic operations on DynamoDB tables](WorkingWithTables.Basics.md)\. 

Tables can be switched to on\-demand mode once every 24 hours\. Creating a table as on\-demand also starts this 24\-hour period\. Tables can be returned to provisioned capacity mode at any time\. For issues to consider when switching read/write capacity modes, see [Considerations when changing read/write Capacity Mode](switching.capacitymode.md)\.

**Topics**
+ [Read request units and write request units](#HowItWorks.requests)
+ [Peak traffic and scaling properties](#HowItWorks.PeakTraffic)
+ [Initial throughput for on\-demand capacity mode](#HowItWorks.InitialThroughput)
+ [Table behavior while switching read/write capacity mode](#HowItWorks.SwitchReadWriteCapacityMode)

### Read request units and write request units<a name="HowItWorks.requests"></a>

For on\-demand mode tables, you don't need to specify how much read and write throughput you expect your application to perform\. DynamoDB charges you for the reads and writes that your application performs on your tables in terms of read request units and write request units\. 

DynamoDB read requests can be either strongly consistent, eventually consistent, or transactional\.
+ A *strongly consistent* read request of an item up to 4 KB requires one read request unit\.
+ An *eventually consistent* read request of an item up to 4 KB requires one\-half read request unit\.
+ A *transactional* read request of an item up to 4 KB requires two read request units\.

If you need to read an item that is larger than 4 KB, DynamoDB needs additional read request units\. The total number of read request units required depends on the item size, and whether you want an eventually consistent or strongly consistent read\. For example, if your item size is 8 KB, you require 2 read request units to sustain one strongly consistent read, 1 read request unit if you choose eventually consistent reads, or 4 read request units for a transactional read request\. 

To learn more about DynamoDB read consistency models, see [Read consistency](HowItWorks.ReadConsistency.md)\. 

**Important**  
 If you perform a read operation on an item that *does not exist*, DynamoDB will still consume read throughput as outlined above\. 

One *write request unit* represents one write for an item up to 1 KB in size\. If you need to write an item that is larger than 1 KB, DynamoDB needs to consume additional write request units\. Transactional write requests require 2 write request units to perform one write for items up to 1 KB\. The total number of write request units required depends on the item size\. For example, if your item size is 2 KB, you require 2 write request units to sustain one write request or 4 write request units for a transactional write request\. 

 For detailed pricing examples and to estimate costs using the pricing calculator, see [Amazon DynamoDB Pricing](https://aws.amazon.com/dynamodb/pricing)\. 

### Peak traffic and scaling properties<a name="HowItWorks.PeakTraffic"></a>

DynamoDB tables using on\-demand capacity mode automatically adapt to your application’s traffic volume\. On\-demand capacity mode instantly accommodates up to double the previous peak traffic on a table\. For example, if your application’s traffic pattern varies between 25,000 and 50,000 strongly consistent reads per second where 50,000 reads per second is the previous traffic peak, on\-demand capacity mode instantly accommodates sustained traffic of up to 100,000 reads per second\. If your application sustains traffic of 100,000 reads per second, that peak becomes your new previous peak, enabling subsequent traffic to reach up to 200,000 reads per second\. 

 If you need more than double your previous peak on table, DynamoDB automatically allocates more capacity as your traffic volume increases to help ensure that your workload does not experience throttling\. However, throttling can occur if you exceed double your previous peak within 30 minutes\. For example, if your application’s traffic pattern varies between 25,000 and 50,000 strongly consistent reads per second where 50,000 reads per second is the previously reached traffic peak, DynamoDB recommends spacing your traffic growth over at least 30 minutes before driving more than 100,000 reads per second\. 

### Initial throughput for on\-demand capacity mode<a name="HowItWorks.InitialThroughput"></a>

If you recently switched an existing table to on\-demand capacity mode for the first time, or if you created a new table with on\-demand capacity mode enabled, the table has the following previous peak settings, even though the table has not served traffic previously using on\-demand capacity mode:

Following are examples of possible scenarios\.
+  ** A provisioned table configured as 100 WCU and 100 RCU\. ** When this table is switched to on\-demand for the first time, DynamoDB will ensure it is scaled out to instantly sustain at least 4,000 write units/sec and 12,000 read units/sec\. 
+  **A provisioned table configured as 8,000 WCU and 24,000 RCU\. **When this table is switched to on\-demand, it will continue to be able to sustain at least 8,000 write units/sec and 24,000 read units/sec at any time\.
+  **A provisioned table configured with 8,000 WCU and 24,000 RCU, that consumed 6,000 write units/sec and 18,000 read units/sec for a sustained period\.** When this table is switched to on\-demand, it will continue to be able to sustain at least 8,000 write units/sec and 24,000 read units/sec\. The previous traffic may further allow the table to sustain much higher levels of traffic without throttling\.
+  **A table previously provisioned with 10,000 WCU and 10,000 RCU, but currently provisioned with 10 RCU and 10 WCU\.** When this table is switched to on\-demand, it will be able to sustain at least 10,000 write units/sec and 10,000 read units/sec\.

### Table behavior while switching read/write capacity mode<a name="HowItWorks.SwitchReadWriteCapacityMode"></a>

When you switch a table from provisioned capacity mode to on\-demand capacity mode, DynamoDB makes several changes to the structure of your table and partitions\. This process can take several minutes\. During the switching period, your table delivers throughput that is consistent with the previously provisioned write capacity unit and read capacity unit amounts\. When switching from on\-demand capacity mode back to provisioned capacity mode, your table delivers throughput consistent with the previous peak reached when the table was set to on\-demand capacity mode\.

## Provisioned mode<a name="HowItWorks.ProvisionedThroughput.Manual"></a>

 If you choose provisioned mode, you specify the number of reads and writes per second that you require for your application\. You can use auto scaling to adjust your table’s provisioned capacity automatically in response to traffic changes\. This helps you govern your DynamoDB use to stay at or below a defined request rate in order to obtain cost predictability\. 

 Provisioned mode is a good option if any of the following are true: 
+ You have predictable application traffic\. 
+ You run applications whose traffic is consistent or ramps gradually\. 
+ You can forecast capacity requirements to control costs\. 

### Read capacity units and write capacity units<a name="HowItWorks.Provisioned"></a>

 For provisioned mode tables, you specify throughput capacity in terms of read capacity units \(RCUs\) and write capacity units \(WCUs\):
+ One *read capacity unit* represents one strongly consistent read per second, or two eventually consistent reads per second, for an item up to 4 KB in size\. Transactional read requests require two read capacity units to perform one read per second for items up to 4 KB\. If you need to read an item that is larger than 4 KB, DynamoDB must consume additional read capacity units\. The total number of read capacity units required depends on the item size, and whether you want an eventually consistent or strongly consistent read\. For example, if your item size is 8 KB, you require 2 read capacity units to sustain one strongly consistent read per second, 1 read capacity unit if you choose eventually consistent reads, or 4 read capacity units for a transactional read request\. For more information, see [Capacity unit consumption for reads](ProvisionedThroughput.md#ItemSizeCalculations.Reads)\.
**Note**  
 To learn more about DynamoDB read consistency models, see [Read consistency](HowItWorks.ReadConsistency.md)\.
+ One *write capacity unit* represents one write per second for an item up to 1 KB in size\. If you need to write an item that is larger than 1 KB, DynamoDB must consume additional write capacity units\. Transactional write requests require 2 write capacity units to perform one write per second for items up to 1 KB\. The total number of write capacity units required depends on the item size\. For example, if your item size is 2 KB, you require 2 write capacity units to sustain one write request per second or 4 write capacity units for a transactional write request\. For more information, see [Capacity unit consumption for writes](ProvisionedThroughput.md#ItemSizeCalculations.Writes)\.

**Important**  
 When calling `DescribeTable` on an on\-demand table, read capacity units and write capacity units are set to 0\.

If your application reads or writes larger items \(up to the DynamoDB maximum item size of 400 KB\), it will consume more capacity units\.

For example, suppose that you create a provisioned table with 6 read capacity units and 6 write capacity units\. With these settings, your application could do the following:
+ Perform strongly consistent reads of up to 24 KB per second \(4 KB × 6 read capacity units\)\.
+ Perform eventually consistent reads of up to 48 KB per second \(twice as much read throughput\)\.
+ Perform transactional read requests of up to 12 KB per second\.
+ Write up to 6 KB per second \(1 KB × 6 write capacity units\)\.
+ Perform transactional write requests of up to 3 KB per second\.

For more information, see [Managing settings on DynamoDB provisioned capacity tables](ProvisionedThroughput.md)\.

*Provisioned throughput* is the maximum amount of capacity that an application can consume from a table or index\. If your application exceeds your provisioned throughput capacity on a table or index, it is subject to request throttling\.

Throttling prevents your application from consuming too many capacity units\. When a request is throttled, it fails with an HTTP 400 code \(`Bad Request`\) and a `ProvisionedThroughputExceededException`\. The AWS SDKs have built\-in support for retrying throttled requests \(see [Error retries and exponential backoff](Programming.Errors.md#Programming.Errors.RetryAndBackoff)\), so you do not need to write this logic yourself\.

You can use the AWS Management Console to monitor your provisioned and actual throughput, and to modify your throughput settings if necessary\.

For detailed pricing examples and to estimate costs using the pricing calculator, see [Amazon DynamoDB Pricing](https://aws.amazon.com/dynamodb/pricing)\.

### DynamoDB auto scaling<a name="HowItWorks.ProvisionedThroughput.AutoScaling"></a>

DynamoDB auto scaling actively manages throughput capacity for tables and global secondary indexes\. With auto scaling, you define a range \(upper and lower limits\) for read and write capacity units\. You also define a target utilization percentage within that range\. DynamoDB auto scaling seeks to maintain your target utilization, even as your application workload increases or decreases\.

With DynamoDB auto scaling, a table or a global secondary index can increase its provisioned read and write capacity to handle sudden increases in traffic, without request throttling\. When the workload decreases, DynamoDB auto scaling can decrease the throughput so that you don't pay for unused provisioned capacity\.

**Note**  
If you use the AWS Management Console to create a table or a global secondary index, DynamoDB auto scaling is enabled by default\.  
You can manage auto scaling settings at any time by using the console, the AWS CLI, or one of the AWS SDKs\.

For more information, see [Managing throughput capacity automatically with DynamoDB auto scaling](AutoScaling.md)\.

### Reserved capacity<a name="HowItWorks.ProvisionedThroughput.ReservedCapacity"></a>

As a DynamoDB customer, you can purchase *reserved capacity* in advance for tables that use the DynamoDB Standard table class, as described at [Amazon DynamoDB Pricing](https://aws.amazon.com/dynamodb/pricing)\. With reserved capacity, you pay a one\-time upfront fee and commit to a minimum provisioned usage level over a period of time\. Your reserved capacity is billed at the hourly reserved capacity rate\. By reserving your read and write capacity units ahead of time, you realize significant cost savings on your provisioned capacity costs\. Any capacity that you provision in excess of your reserved capacity is billed at standard provisioned capacity rates\.

Reserved capacity discounts are applied first to the account that purchased the reserved capacity\. Any unused reserved capacity discount is then applied to other accounts in the same AWS organization as the purchasing account\. You can turn off Reserved Instance discount sharing on the ** Preferences** page on the Billing and Cost Management console\. For more information, see [ Turning off reserved instances and Savings Plans discount sharing](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/ri-turn-off.html)\.

**Note**  
Reserved capacity is not available for replicated write capacity units\. Reserved Capacity is applied only to the region in which it was purchased\. Reserved capacity is also not available for tables using the DynamoDB Standard\-IA table class or on\-demand capacity mode\.

To manage reserved capacity, go to the [DynamoDB console](https://console.aws.amazon.com/dynamodb) and choose **Reserved Capacity**\.

**Note**  
You can prevent users from viewing or purchasing reserved capacity, while still allowing them to access the rest of the console\. For more information, see "Grant Permissions to Prevent Purchasing of Reserved Capacity Offerings" in [Identity and Access Management for Amazon DynamoDB](security-iam.md)\.

For more information about specific pricing see [Amazon DynamoDB Pricing](https://aws.amazon.com/dynamodb/pricing)\.