# Change Data Capture for Kinesis Data Streams with DynamoDB<a name="kds"></a>

Kinesis Data Streams for DynamoDB captures item\-level modifications in any DynamoDB table and replicates them to a [Kinesis data stream](https://docs.aws.amazon.com/streams/latest/dev/introduction.html) of your choice\. Your applications can access the Kinesis data stream and view the item\-level changes in near\-real time\.

Streaming your DynamoDB data to a Kinesis data stream enables you to continuously capture and store terabytes of data per hour\. Kinesis Data Streams enables you to take advantage of longer data retention time, enhanced fan\-out capability to more than two simultaneous consumer applications, and additional audit and security transparency\. Kinesis Data Streams also gives you access to other Kinesis services such as [Amazon Kinesis Data Firehose](https://docs.aws.amazon.com/firehose/latest/dev/what-is-this-service.html) and [Amazon Kinesis Data Analytics](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/what-is.html)\. This enables you to build applications to power real\-time dashboards, generate alerts, implement dynamic pricing and advertising, and perform sophisticated data analytics, such as applying machine learning algorithms\.

**Note**  
Using Kinesis Data Streams for DynamoDB is subject to both [Kinesis Data Streams pricing](https://aws.amazon.com/kinesis/data-streams/pricing/) for the data stream and [DynamoDB pricing](https://aws.amazon.com/dynamodb/pricing/) for the source table\. 

## How Change Data Capture Works for Kinesis Data Streams<a name="kds_howitworks"></a>

Amazon Kinesis Data Streams for Amazon DynamoDB operates asynchronously, so there is no performance impact on a table if a stream is enabled\. Whenever items are created, updated, or deleted in the table, DynamoDB sends a data record to Kinesis\. The record contains information about a data modification to a single item in a DynamoDB table\. Specifically, a data record contains the primary key attribute of the item that was modified, together with the "before" and "after" images of the modified item\.

Data records are published in near\-real time to your Kinesis data stream\. After they are written to the Kinesis data stream, you can read the written records as you would read any other record from any Kinesis data stream\. For example, you can use the Kinesis Client Library, AWS Lambda, or call the Kinesis Data Streams API\. For more information, see [Reading Data from Amazon Kinesis Data Streams](https://docs.aws.amazon.com/streams/latest/dev/building-consumers.html) in the Amazon Kinesis Data Streams Developer Guide\.

The stream records stored in your Kinesis data stream is encrypted at rest\. For more information, see [Data Protection in Amazon Kinesis Data Streams](https://docs.aws.amazon.com/streams/latest/dev/server-side-encryption.html)\.

For each item that is modified in a DynamoDB table, the Kinesis data stream records may appear in a different sequence than the actual modifications to the item\. Stream records may appear more than once in the Kinesis data stream\. You can use the timestamp attribute `ApproximateCreationDateTime` on each record to identify the actual order in which item modifications occurred, and to identify duplicate records in the stream\.

DynamoDB charges for change data capture for Kinesis Data Streams in change data capture units\. DynamoDB charges one change data capture unit for each item write \(up to 1 KB\)\. Writes larger than 1 KB require additional change data capture units\. DynamoDB calculates change data capture units based on the larger of the “before” and “after” images that constitute a change record, using the same logic as [capacity unit consumption for write operations](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ProvisionedThroughput.html#ItemSizeCalculations.Writes)\. You do not need to provision capacity throughput for change data capture units, similar to how DynamoDB [on\-demand](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.ReadWriteCapacityMode.html#HowItWorks.OnDemand) mode works\. 

The following sections describe the core concepts and behavior of Amazon Kinesis Data Streams for Amazon DynamoDB\.

**Topics**
+ [Enabling Amazon Kinesis Data Streams for Amazon DynamoDB](#kds_howitworks.enabling)
+ [Shard Management Considerations for Amazon Kinesis Data Streams](#kds_howitworks.shardmanagment)
+ [Monitoring Amazon Kinesis Data Streams for Amazon DynamoDB](#kds_howitworks.monitoring)

### Enabling Amazon Kinesis Data Streams for Amazon DynamoDB<a name="kds_howitworks.enabling"></a>

You can enable or disable streaming to Kinesis on an existing DynamoDB table by using the AWS Management Console, the AWS SDK, or the AWS Command Line Interface \(AWS CLI\)\.
+ You can only enable Amazon Kinesis Data Streams for Amazon DynamoDB streaming to a Kinesis data stream from the same AWS account and AWS Region as your table\. 
+ You can only enable Amazon Kinesis Data Streams for Amazon DynamoDB streaming from a DynamoDB table to one Kinesis data stream\.

### Shard Management Considerations for Amazon Kinesis Data Streams<a name="kds_howitworks.shardmanagment"></a>

A [shard](https://docs.aws.amazon.com/streams/latest/dev/key-concepts.html) is the base throughput unit of an Amazon Kinesis data stream\. You need to provision an appropriate number of shards on your Kinesis data stream to accommodate the change data capture records from DynamoDB\.

The following input values help you determine the number of shards needed for the Kinesis data stream to support your DynamoDB table:
+ The average size of your DynamoDB table’s record in bytes \(`average_record_size_in_bytes`\)\.
+ The maximum number of write operations you perform on your DynamoDB table per second\. This includes create, delete, and update data operations performed by your applications as well as automatically generated operations, such as Time to Live generated deletes \(`write_throughput`\)\.
+ The percentage of update and overwrite operations you perform on your table compared to create or delete operations \(`percentage_of_updates`\)\. Update and overwrite operations replicate both the old and new images of the modified item to the stream, thus generating twice the DynamoDB item size\.

You can approximate the number of shards \(`number_of_shards`\) needed for your DynamoDB table by using the input values in the following formula:

```
number_of_shards = ceiling( ((write_throughput * (1+percentage_of_updates) * average_record_size_in_bytes) /1024 /1024), 1)
```

This formula reflects only the shards needed to accommodate your DynamoDB streaming throughput\. It does not represent the total number of shards needed in your Kinesis data stream, such as shards required to support Kinesis data stream consumers\. To learn more about determining the size of a Kinesis data stream, see [Determining the Initial Size of a Kinesis Data Stream](https://docs.aws.amazon.com/streams/latest/dev/amazon-kinesis-streams.html#how-do-i-size-a-stream)\.

For example, let us say you have a maximum throughput of 40 write operations/second \(`write_throughput`\) with an average record size of 1285 bytes \(`average_record_size_in_bytes`\)\. If 25% of those write operations are update operations \(`percentage_of_updates`\) then you will need 2 shards \(`number_of_shards`\) to accommodate your DynamoDB streaming throughput: ceiling\( \(\(40 \* \(1\+25\) \* 1285 \)/ 1024 / 1024\), 1 \)\.

### Monitoring Amazon Kinesis Data Streams for Amazon DynamoDB<a name="kds_howitworks.monitoring"></a>

DynamoDB provides several Amazon CloudWatch metrics to help you monitor replication of change data capture to Kinesis\. For a full list of CloudWatch metrics , see [DynamoDB Metrics and Dimensions](metrics-dimensions.md)\.

We recommend that you monitor the following items both during stream enabling and in production to determine whether your stream has sufficient capacity:
+ `ThrottledPutRecordCount`: The number of records that failed to replicate to the Kinesis data stream because of insufficient Kinesis data stream capacity\. The `ThrottledPutRecordCount` should remain as low as possible, though you might experience some throttling during exceptional usage peaks\. DynamoDB retries putting throttled records to the Kinesis data stream, but this might result in higher replication latency\. If you experience excessive and regular throttling, you may need to increase the number of Kinesis stream shards proportionally to the observed write throughput of your table\. To learn more about determining the size of a Kinesis data stream, see [Determining the Initial Size of a Kinesis Data Stream](https://docs.aws.amazon.com/streams/latest/dev/amazon-kinesis-streams.html#how-do-i-size-a-stream)\.
+ `AgeOfOldestUnreplicatedRecord`: The elapsed time since the oldest item\-level change yet to replicate to the Kinesis data stream appeared in the DynamoDB table\. Under normal operation, `AgeOfOldestUnreplicatedRecord` should be in the order of milliseconds\. This number grows based on unsuccessful replication attempts\. DynamoDB continuously retries unsuccessful replication attempts when there is replication failure\. You might need to adjust your Kinesis data stream capacity to keep this metric as low as possible\.

You can create Amazon CloudWatch alarms that send an Amazon Simple Notification Service \(Amazon SNS\) message for notification when any of the preceding metrics exceed a specific threshold\. For more information, see [Creating CloudWatch Alarms to Monitor DynamoDB](creating-alarms.md)\.