# Best Practices for Designing and Using Partition Keys Effectively<a name="bp-partition-key-design"></a>

The primary key that uniquely identifies each item in an Amazon DynamoDB table can be simple \(a partition key only\) or composite \(a partition key combined with a sort key\)\. 

Generally speaking, you should design your application for uniform activity across all logical partition keys in the table and its secondary indexes\. You can determine the access patterns that your application requires, and estimate the total read capacity units \(RCU\) and write capacity units \(WCU\) that each table and secondary index requires\. 

DynamoDB supports your access patterns using the throughput that you provisioned as long as the traffic against a given partition does not exceed 3,000 RCUs or 1,000 WCUs\.

**Topics**
+ [Using Burst Capacity Effectively](#bp-partition-key-throughput-bursting)
+ [Understanding DynamoDB Adaptive Capacity](#bp-partition-key-partitions-adaptive)
+ [Designing Partition Keys to Distribute Your Workload Evenly](bp-partition-key-uniform-load.md)
+ [Using Write Sharding to Distribute Workloads Evenly](bp-partition-key-sharding.md)
+ [Distributing Write Activity Efficiently During Data Upload](bp-partition-key-data-upload.md)

## Using Burst Capacity Effectively<a name="bp-partition-key-throughput-bursting"></a>

DynamoDB provides some flexibility in your per\-partition throughput provisioning by providing *burst capacity*\. Whenever you're not fully using a partition's throughput, DynamoDB reserves a portion of that unused capacity for later *bursts* of throughput to handle usage spikes\.

DynamoDB currently retains up to 5 minutes \(300 seconds\) of unused read and write capacity\. During an occasional burst of read or write activity, these extra capacity units can be consumed quickly—even faster than the per\-second provisioned throughput capacity that you've defined for your table\.

DynamoDB can also consume burst capacity for background maintenance and other tasks without prior notice\.

Note that these burst capacity details might change in the future\.

## Understanding DynamoDB Adaptive Capacity<a name="bp-partition-key-partitions-adaptive"></a>

*Adaptive capacity* is a feature that enables DynamoDB to run imbalanced workloads indefinitely\. It minimizes throttling due to throughput exceptions\. It also helps you reduce costs by enabling you to provision only the throughput capacity that you need\.

Adaptive capacity is enabled automatically for every DynamoDB table, at no additional cost\. You don't need to explicitly enable or disable it\.

**Topics**
+ [Boost Throughput Capacity to High\-Traffic Partitions](#bp-partition-key-partitions-adaptive-boost)
+ [Isolate Frequently Accessed Items](#bp-partition-key-partitions-adaptive-split)

### Boost Throughput Capacity to High\-Traffic Partitions<a name="bp-partition-key-partitions-adaptive-boost"></a>

It's not always possible to distribute read and write activity evenly\. When data access is imbalanced, a "hot" partition can receive a higher volume of read and write traffic compared to other partitions\. In extreme cases, throttling can occur if a single partition receives more than 3,000 RCUs or 1,000 WCUs\.

 To better accommodate uneven access patterns, DynamoDB adaptive capacity enables your application to continue reading and writing to hot partitions without being throttled, provided that traffic does not exceed your table’s total provisioned capacity or the partition maximum capacity\. Adaptive capacity works by automatically and instantly increasing throughput capacity for partitions that receive more traffic\. 

The following diagram illustrates how adaptive capacity works\. The example table is provisioned with 400 WCUs evenly shared across four partitions, allowing each partition to sustain up to 100 WCUs per second\. Partitions 1, 2, and 3 each receives write traffic of 50 WCU/sec\. Partition 4 receives 150 WCU/sec\. This hot partition can accept write traffic while it still has unused burst capacity, but eventually it throttles traffic that exceeds 100 WCU/sec\.

DynamoDB adaptive capacity responds by increasing partition 4's capacity so that it can sustain the higher workload of 150 WCU/sec without being throttled\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/adaptive-capacity.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

### Isolate Frequently Accessed Items<a name="bp-partition-key-partitions-adaptive-split"></a>

If your application drives disproportionately high traffic to one or more items, adaptive capacity rebalances your partitions such that frequently accessed items don't reside on the same partition\. This isolation of frequently accessed items reduces the likelihood of request throttling due to your workload exceeding the throughput quota on a single partition\. 

 If your application drives consistently high traffic to a single item, adaptive capacity might rebalance your data such that a partition contains only that single, frequently accessed item\. In this case, DynamoDB can deliver throughput up to the partition maximum of 3,000 RCUs or 1,000 WCUs to that single item’s primary key\.

**Note**  
This isolation functionality is not available for  
Tables that have a [local secondary index ](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/LSI.html)\.
Tables using [Provisioned Read/Write Capacity Mode](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.ReadWriteCapacityMode.html#HowItWorks.ProvisionedThroughput.Manual) that have enabled [DynamoDB Streams](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Streams.html)\.