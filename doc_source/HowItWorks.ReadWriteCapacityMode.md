# Read/Write Capacity Mode<a name="HowItWorks.ReadWriteCapacityMode"></a>

Amazon DynamoDB has two read/write capacity modes for processing reads and writes on your tables: 
+  On\-demand 
+  Provisioned \(default, free\-tier eligible\) 

The read/write capacity mode controls how you are charged for read and write throughput and how you manage capacity\. You can set the read/write capacity mode when creating a table or you can change it later\.

Secondary indexes inherit the read/write capacity mode from the base table\. For more information, see [Considerations When Changing Read/Write Capacity Mode](switching.capacitymode.md)\.

**Topics**
+ [On\-Demand Mode](#HowItWorks.OnDemand)
+ [Provisioned Mode](#HowItWorks.ProvisionedThroughput.Manual)

## On\-Demand Mode<a name="HowItWorks.OnDemand"></a>

Amazon DynamoDB on\-demand is a flexible billing option capable of serving thousands of requests per second without capacity planning\. DynamoDB on\-demand offers pay\-per\-request pricing for read and write requests so that you pay only for what you use\. 

 When you choose on\-demand mode, DynamoDB instantly accommodates your workloads as they ramp up or down to any previously reached traffic level\. If a workload’s traffic level hits a new peak, DynamoDB adapts rapidly to accommodate the workload\. Tables that use on\-demand mode deliver the same single\-digit millisecond latency, service\-level agreement \(SLA\) commitment, and security that DynamoDB already offers\. You can choose on\-demand for both new and existing tables and you can continue using the existing DynamoDB APIs without changing code\.

On\-demand mode is a good option if any of the following are true: 
+ You create new tables with unknown workloads\. 
+ You have unpredictable application traffic\. 
+ You prefer the ease of paying for only what you use\. 

 The request rate is only limited by the DynamoDB throughput default table quotas, but it can be raised upon request\. For more information, see [Throughput Default Quotas](ServiceQuotas.md#default-limits-throughput)\. 

 To get started with on\-demand, you can create or update a table to use on\-demand mode\. For more information, see [Basic Operations on DynamoDB Tables](WorkingWithTables.Basics.md)\. 

You can switch between read/write capacity modes once every 24 hours\. For issues you should consider when switching read/write capacity modes, see [Considerations When Changing Read/Write Capacity Mode](switching.capacitymode.md)\.

**Topics**
+ [Read Request Units and Write Request Units](#HowItWorks.requests)
+ [Peak Traffic and Scaling Properties](#HowItWorks.PeakTraffic)
+ [Initial Throughput for On\-Demand Capacity Mode](#HowItWorks.InitialThroughput)
+ [Table Behavior while Switching Read/Write Capacity Mode](#HowItWorks.SwitchReadWriteCapacityMode)

### Read Request Units and Write Request Units<a name="HowItWorks.requests"></a>

 For on\-demand mode tables, you don't need to specify how much read and write throughput you expect your application to perform\. DynamoDB charges you for the reads and writes that your application performs on your tables in terms of read request units and write request units\. 
+ One *read request unit* represents one strongly consistent read request, or two eventually consistent read requests, for an item up to 4 KB in size\. Two read request units represent one transactional read for items up to 4 KB\. If you need to read an item that is larger than 4 KB, DynamoDB needs additional read request units\. The total number of read request units required depends on the item size, and whether you want an eventually consistent or strongly consistent read\. For example, if your item size is 8 KB, you require 2 read request units to sustain one strongly consistent read, 1 read request unit if you choose eventually consistent reads, or 4 read request units for a transactional read request\. 
**Note**  
 To learn more about DynamoDB read consistency models, see [Read Consistency](HowItWorks.ReadConsistency.md)\. 
+ One *write request unit* represents one write for an item up to 1 KB in size\. If you need to write an item that is larger than 1 KB, DynamoDB needs to consume additional write request units\. Transactional write requests require 2 write request units to perform one write for items up to 1 KB\. The total number of write request units required depends on the item size\. For example, if your item size is 2 KB, you require 2 write request units to sustain one write request or 4 write request units for a transactional write request\. 

 For a list of AWS Regions where DynamoDB on\-demand is available, see [Amazon DynamoDB Pricing](https://aws.amazon.com/dynamodb/pricing)\. 

### Peak Traffic and Scaling Properties<a name="HowItWorks.PeakTraffic"></a>

DynamoDB tables using on\-demand capacity mode automatically adapt to your application’s traffic volume\. On\-demand capacity mode instantly accommodates up to double the previous peak traffic on a table\. For example, if your application’s traffic pattern varies between 25,000 and 50,000 strongly consistent reads per second where 50,000 reads per second is the previous traffic peak, on\-demand capacity mode instantly accommodates sustained traffic of up to 100,000 reads per second\. If your application sustains traffic of 100,000 reads per second, that peak becomes your new previous peak, enabling subsequent traffic to reach up to 200,000 reads per second\. 

 If you need more than double your previous peak on table, DynamoDB automatically allocates more capacity as your traffic volume increases to help ensure that your workload does not experience throttling\. However, throttling can occur if you exceed double your previous peak within 30 minutes\. For example, if your application’s traffic pattern varies between 25,000 and 50,000 strongly consistent reads per second where 50,000 reads per second is the previously reached traffic peak, DynamoDB recommends spacing your traffic growth over at least 30 minutes before driving more than 100,000 reads per second\. 

### Initial Throughput for On\-Demand Capacity Mode<a name="HowItWorks.InitialThroughput"></a>

If you recently switched an existing table to on\-demand capacity mode for the first time, or if you created a new table with on\-demand capacity mode enabled, the table has the following previous peak settings, even though the table has not served traffic previously using on\-demand capacity mode:
+  ** Newly created table with on\-demand capacity mode:** The previous peak is 2,000 write request units or 6,000 read request units\. You can drive up to double the previous peak immediately, which enables newly created on\-demand tables to serve up to 4,000 write request units and 12,000 read request units\.
+  **Existing table switched to on\-demand capacity mode:** The previous peak is half the maximum write capacity units and read capacity units provisioned since the table was created, or the settings for a newly created table with on\-demand capacity mode, whichever is higher\. In other words, your table will deliver at least as much throughput as it did prior to switching to on\-demand capacity mode\. 

### Table Behavior while Switching Read/Write Capacity Mode<a name="HowItWorks.SwitchReadWriteCapacityMode"></a>

When you switch a table from provisioned capacity mode to on\-demand capacity mode, DynamoDB makes several changes to the structure of your table and partitions\. This process can take several minutes\. During the switching period, your table delivers throughput that is consistent with the previously provisioned write capacity unit and read capacity unit amounts\. When switching from on\-demand capacity mode back to provisioned capacity mode, your table delivers throughput consistent with the previous peak reached when the table was set to on\-demand capacity mode\.

## Provisioned Mode<a name="HowItWorks.ProvisionedThroughput.Manual"></a>

 If you choose provisioned mode, you specify the number of reads and writes per second that you require for your application\. You can use auto scaling to adjust your table’s provisioned capacity automatically in response to traffic changes\. This helps you govern your DynamoDB use to stay at or below a defined request rate in order to obtain cost predictability\. 

 Provisioned mode is a good option if any of the following are true: 
+ You have predictable application traffic\. 
+ You run applications whose traffic is consistent or ramps gradually\. 
+ You can forecast capacity requirements to control costs\. 

### Read Capacity Units and Write Capacity Units<a name="HowItWorks.Provisioned"></a>

 For provisioned mode tables, you specify throughput capacity in terms of read capacity units \(RCUs\) and write capacity units \(WCUs\):
+ One *read capacity unit* represents one strongly consistent read per second, or two eventually consistent reads per second, for an item up to 4 KB in size\. Transactional read requests require two read capacity units to perform one read per second for items up to 4 KB\. If you need to read an item that is larger than 4 KB, DynamoDB must consume additional read capacity units\. The total number of read capacity units required depends on the item size, and whether you want an eventually consistent or strongly consistent read\. For example, if your item size is 8 KB, you require 2 read capacity units to sustain one strongly consistent read per second, 1 read capacity unit if you choose eventually consistent reads, or 4 read capacity units for a transactional read request\. For more information, see [Capacity Unit Consumption for Reads](ProvisionedThroughput.md#ItemSizeCalculations.Reads)\.
**Note**  
 To learn more about DynamoDB read consistency models, see [Read Consistency](HowItWorks.ReadConsistency.md)\.
+ One *write capacity unit* represents one write per second for an item up to 1 KB in size\. If you need to write an item that is larger than 1 KB, DynamoDB must consume additional write capacity units\. Transactional write requests require 2 write capacity units to perform one write per second for items up to 1 KB\. The total number of write capacity units required depends on the item size\. For example, if your item size is 2 KB, you require 2 write capacity units to sustain one write request per second or 4 write capacity units for a transactional write request\. For more information, see [Capacity Unit Consumption for Writes](ProvisionedThroughput.md#ItemSizeCalculations.Writes)\.

**Important**  
 When calling `DescribeTable` on an on\-demand table, read capacity units and write capacity units are set to 0\.

If your application reads or writes larger items \(up to the DynamoDB maximum item size of 400 KB\), it will consume more capacity units\.

For example, suppose that you create a provisioned table with 6 read capacity units and 6 write capacity units\. With these settings, your application could do the following:
+ Perform strongly consistent reads of up to 24 KB per second \(4 KB × 6 read capacity units\)\.
+ Perform eventually consistent reads of up to 48 KB per second \(twice as much read throughput\)\.
+ Perform transactional read requests of up to 12 KB per second\.
+ Write up to 6 KB per second \(1 KB × 6 write capacity units\)\.
+ Perform transactional write requests of up to 3 KB per second\.

For more information, see [Managing Settings on DynamoDB Provisioned Capacity Tables](ProvisionedThroughput.md)\.

*Provisioned throughput* is the maximum amount of capacity that an application can consume from a table or index\. If your application exceeds your provisioned throughput capacity on a table or index, it is subject to request throttling\.

Throttling prevents your application from consuming too many capacity units\. When a request is throttled, it fails with an HTTP 400 code \(`Bad Request`\) and a `ProvisionedThroughputExceededException`\. The AWS SDKs have built\-in support for retrying throttled requests \(see [Error Retries and Exponential Backoff](Programming.Errors.md#Programming.Errors.RetryAndBackoff)\), so you do not need to write this logic yourself\.

You can use the AWS Management Console to monitor your provisioned and actual throughput, and to modify your throughput settings if necessary\.

### DynamoDB Auto Scaling<a name="HowItWorks.ProvisionedThroughput.AutoScaling"></a>

DynamoDB auto scaling actively manages throughput capacity for tables and global secondary indexes\. With auto scaling, you define a range \(upper and lower limits\) for read and write capacity units\. You also define a target utilization percentage within that range\. DynamoDB auto scaling seeks to maintain your target utilization, even as your application workload increases or decreases\.

With DynamoDB auto scaling, a table or a global secondary index can increase its provisioned read and write capacity to handle sudden increases in traffic, without request throttling\. When the workload decreases, DynamoDB auto scaling can decrease the throughput so that you don't pay for unused provisioned capacity\.

**Note**  
If you use the AWS Management Console to create a table or a global secondary index, DynamoDB auto scaling is enabled by default\.  
You can manage auto scaling settings at any time by using the console, the AWS CLI, or one of the AWS SDKs\.

For more information, see [Managing Throughput Capacity Automatically with DynamoDB Auto Scaling](AutoScaling.md)\.

### Reserved Capacity<a name="HowItWorks.ProvisionedThroughput.ReservedCapacity"></a>

As a DynamoDB customer, you can purchase *reserved capacity* in advance for tables that use the DynamoDB Standard table class, as described at [Amazon DynamoDB Pricing](https://aws.amazon.com/dynamodb/pricing)\. With reserved capacity, you pay a one\-time upfront fee and commit to a minimum provisioned usage level over a period of time\. Your reserved capacity is billed at the hourly reserved capacity rate\. By reserving your read and write capacity units ahead of time, you realize significant cost savings on your provisioned capacity costs\. Any capacity that you provision in excess of your reserved capacity is billed at standard provisioned capacity rates\.

**Note**  
Reserved capacity is not available for replicated write capacity units\. Reserved capacity is also not available for tables using the DynamoDB Standard\-IA table class or on\-demand capacity mode\.

To manage reserved capacity, go to the [DynamoDB console](https://console.aws.amazon.com/dynamodb) and choose **Reserved Capacity**\.

**Note**  
You can prevent users from viewing or purchasing reserved capacity, while still allowing them to access the rest of the console\. For more information, see "Grant Permissions to Prevent Purchasing of Reserved Capacity Offerings" in [Identity and Access Management in Amazon DynamoDB](authentication-and-access-control.md)\.