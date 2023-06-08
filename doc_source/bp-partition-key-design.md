# Best practices for designing and using partition keys effectively<a name="bp-partition-key-design"></a>

The primary key that uniquely identifies each item in an Amazon DynamoDB table can be simple \(a partition key only\) or composite \(a partition key combined with a sort key\)\. 

Generally speaking, you should design your application for uniform activity across all logical partition keys in the table and its secondary indexes\. You can determine the access patterns that your application requires, and read and write units that each table and secondary index requires\. 

By default, every partition in the table will strive to deliver the full capacity of 3,000 RCU and 1,000 WCU\. The total throughput across all partitions in the table may be constrained by the provisioned throughput in provisioned mode, or by the table level throughput limit in on\-demand mode\. See [Service Quotas](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ServiceQuotas.html) for more information\.

**Topics**
+ [Using burst capacity effectively](#bp-partition-key-throughput-bursting)
+ [Understanding DynamoDB adaptive capacity](#bp-partition-key-partitions-adaptive)
+ [Designing partition keys to distribute your workload](bp-partition-key-uniform-load.md)
+ [Using write sharding to distribute workloads evenly](bp-partition-key-sharding.md)
+ [Distributing write activity efficiently during data upload](bp-partition-key-data-upload.md)

## Using burst capacity effectively<a name="bp-partition-key-throughput-bursting"></a>

DynamoDB provides some flexibility for your throughput provisioning with *burst capacity*\. Whenever you're not fully using your available throughput, DynamoDB reserves a portion of that unused capacity for later *bursts* of throughput to handle usage spikes\.

DynamoDB currently retains up to 5 minutes \(300 seconds\) of unused read and write capacity\. During an occasional burst of read or write activity, these extra capacity units can be consumed quickly—even faster than the per\-second provisioned throughput capacity that you've defined for your table\.

DynamoDB can also consume burst capacity for background maintenance and other tasks without prior notice\.

Note that these burst capacity details might change in the future\.

## Understanding DynamoDB adaptive capacity<a name="bp-partition-key-partitions-adaptive"></a>

*Adaptive capacity* is a feature that enables DynamoDB to run imbalanced workloads indefinitely\. It minimizes throttling due to throughput exceptions\. It also helps you reduce costs by enabling you to provision only the throughput capacity that you need\.

Adaptive capacity is enabled automatically for every DynamoDB table, at no additional cost\. You don't need to explicitly enable or disable it\.

**Topics**
+ [Boost throughput capacity to high\-traffic partitions](#bp-partition-key-partitions-adaptive-boost)
+ [Isolate frequently accessed items](#bp-partition-key-partitions-adaptive-split)

### Boost throughput capacity to high\-traffic partitions<a name="bp-partition-key-partitions-adaptive-boost"></a>

It's not always possible to distribute read and write activity evenly\. When data access is imbalanced, a "hot" partition can receive a higher volume of read and write traffic compared to other partitions\. As read and write operations on a partition are managed independently, throttling will occur if a single partition receives more than 3000 read operation or more than 1000 write operations\. 

 To better accommodate uneven access patterns, DynamoDB adaptive capacity enables your application to continue reading and writing to hot partitions without being throttled, provided that traffic does not exceed your table’s total provisioned capacity or the partition maximum capacity\. Adaptive capacity works by automatically and instantly increasing throughput capacity for partitions that receive more traffic\. 

The following diagram illustrates how adaptive capacity works\. The example table is provisioned with 400 WCUs evenly shared across four partitions, allowing each partition to sustain up to 100 WCUs per second\. Partitions 1, 2, and 3 each receives write traffic of 50 WCU/sec\. Partition 4 receives 150 WCU/sec\. This hot partition can accept write traffic while it still has unused burst capacity, but eventually it throttles traffic that exceeds 100 WCU/sec\.

DynamoDB adaptive capacity responds by increasing partition 4's capacity so that it can sustain the higher workload of 150 WCU/sec without being throttled\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/adaptive-capacity.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

### Isolate frequently accessed items<a name="bp-partition-key-partitions-adaptive-split"></a>

If your application drives disproportionately high traffic to one or more items, adaptive capacity rebalances your partitions such that frequently accessed items don't reside on the same partition\. This isolation of frequently accessed items reduces the likelihood of request throttling due to your workload exceeding the throughput quota on a single partition\. You can also break up an item collection into segments by sort key, as long as the item collection isn't traffic that is tracked by a monotonic increase or decrease of the sort key\.

 If your application drives consistently high traffic to a single item, adaptive capacity might rebalance your data so that a partition contains only that single, frequently accessed item\. In this case, DynamoDB can deliver throughput up to the partition maximum of 3,000 RCUs and 1,000 WCUs to that single item’s primary key\. Adaptive capacity will not split item collections across multiple partitions of the table when there is a [local secondary index ](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/LSI.html) on the table\.

**Note**  
This isolation functionality is not available for tables using [Provisioned Read/Write Capacity Mode](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.ReadWriteCapacityMode.html#HowItWorks.ProvisionedThroughput.Manual) that have enabled [DynamoDB Streams](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Streams.html)\.