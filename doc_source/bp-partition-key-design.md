# Best Practices for Designing and Using Partition Keys Effectively<a name="bp-partition-key-design"></a>

The primary key that uniquely identifies each item in a DynamoDB table can be simple \(a partition key only\) or composite \(a partition key combined with a sort key\)\.

Generally speaking, you should design your application for uniform activity across all logical Partition\-Keys in the Table and the Secondary index\. You can determine the access patterns that your application requires, and estimate the total RCUs and WCUs that each table and secondary Index requires\.

As traffic starts to flow, DynamoDB automatically supports your access patterns using the throughput you have provisioned, as long as the traffic against a given partition key does not exceed 3000 RCUs or 1000 WCUs\.

**Topics**
+ [Using Burst Capacity Effectively](#bp-partition-key-throughput-bursting)
+ [Understanding DynamoDB Adaptive Capacity](#bp-partition-key-partitions-adaptive)
+ [Designing Partition Keys to Distribute Your Workload Evenly](bp-partition-key-uniform-load.md)
+ [Using Write Sharding to Distribute Workloads Evenly](bp-partition-key-sharding.md)
+ [Distributing Write Activity Efficiently During Data Upload](bp-partition-key-data-upload.md)

## Using Burst Capacity Effectively<a name="bp-partition-key-throughput-bursting"></a>

DynamoDB provides some flexibility in your per\-partition throughput provisioning by providing **burst capacity**, as follows\. Whenever you are not fully using a partition's throughput, DynamoDB reserves a portion of that unused capacity for later *bursts* of throughput to handle usage spikes\.

DynamoDB currently retains up to five minutes \(300 seconds\) of unused read and write capacity\. During an occasional burst of read or write activity, these extra capacity units can be consumed quickly—even faster than the per\-second provisioned throughput capacity that you've defined for your table\.

**However, don't design your application so that it relies on burst capacity being available at all times\.** DynamoDB can also consume burst capacity for background maintenance and other tasks without prior notice\.

Note that these details of burst capacity might change in the future\.

## Understanding DynamoDB Adaptive Capacity<a name="bp-partition-key-partitions-adaptive"></a>

It's not always possible to distribute read and write activity evenly all the time\. When data access is imbalanced, a "hot" partition can receive such a high amount of read and write traffic compared to other partitions that throttling occurs\.

To avoid this situation, DynamoDB *adaptive capacity* enables your application to continue to perform read and write operations in a hot partition without being throttled, provided that your table's total provisioned capacity is not exceeded\. With adaptive capacity, DynamoDB becomes more tolerant of imbalanced workloads\.

Adaptive capacity is enabled automatically for every DynamoDB table, so you don't need to explicitly enable or disable it\. Although it's useful for handling special situations, you should not rely on it for handling normal anticipated workloads\.

**Important: Avoid relying on adaptive capacity to handle normal anticipated workloads\.**
+ DynamoDB provides adaptive capacity only on a best\-effort basis\.
+ There is typically a 5\-minute to 30\-minute interval between the time throttling of a hot partition begins and the time that adaptive capacity becomes active for that partition\.

The following diagram illustrates how adaptive capacity works\. The example table is provisioned with 400 write\-capacity units \(WCUs\) evenly shared across four partitions, allowing each partition to sustain up to 100 WCUs per second\. Partitions 1, 2, and 3 each receive write traffic of 50 WCU/sec\. Partition 4 receives 150 WCU/sec\. This hot partition can accept write traffic while it still has unused burst capacity, but eventually it will throttle traffic that exceeds 100 WCU/sec\.

DynamoDB adaptive capacity responds by increasing partition 4's capacity so that it can sustain the higher workload of 150 WCU/sec without being throttled\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/adaptive-capacity.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)