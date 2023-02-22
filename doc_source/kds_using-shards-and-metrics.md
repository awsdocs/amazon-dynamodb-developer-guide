# Configuring shards and monitoring change data capture with Kinesis Data Streams in DynamoDB<a name="kds_using-shards-and-metrics"></a>

## Shard management considerations for Kinesis Data Streams<a name="kds_using-shards-and-metrics.shardmanagment"></a>

A Kinesis data stream counts its throughput in [shards](https://docs.aws.amazon.com/streams/latest/dev/key-concepts.html)\. You will need to provision an appropriate number of shards on your Kinesis data stream to accommodate the change data capture records from DynamoDB\. To determine the number of shards that the Kinesis data stream will need to support your DynamoDB table, consider the following:
+ The average size of your DynamoDB table’s record in bytes \(`average_record_size_in_bytes`\)\.
+ The maximum number of write operations that your DynamoDB table will perform per second\. This includes create, delete, and update data operations performed by your applications, as well as automatically generated operations like Time to Live generated deletes \(`write_throughput`\)\.
+ The percentage of update and overwrite operations that you perform on your table, as compared to create or delete operations \(`percentage_of_updates`\)\. Keep in mind that update and overwrite operations replicate both the old and new images of the modified item to the stream\. This generates twice the DynamoDB item size\.

You can approximate the number of shards \(`number_of_shards`\) needed for your DynamoDB table's throughput with this formula:

```
number_of_shards = ceiling( ((write_throughput * (1+percentage_of_updates) * average_record_size_in_bytes) /1024 /1024), 1)
```

For example, you might have a maximum throughput of 40 write operations/second \(`write_throughput`\) with an average record size of 1285 bytes \(`average_record_size_in_bytes`\)\. If 25 percent of those write operations are update operations \(`percentage_of_updates`\), then you will need two shards \(`number_of_shards`\) to accommodate your DynamoDB streaming throughput: ceiling\( \(\(40 \* \(1\+\(25/100\) \* 1285 \)/ 1024 / 1024\), 1 \)\.

Note that this formula reflects only the shards that are needed to accommodate your DynamoDB streaming throughput\. It doesn't represent the total number of shards needed in your Kinesis data stream, such as shards required to support Kinesis data stream consumers\. 

To learn more about determining the size of a Kinesis data stream, see [Determining the Initial Size of a Kinesis Data Stream](https://docs.aws.amazon.com/streams/latest/dev/amazon-kinesis-streams.html#how-do-i-size-a-stream)\.

## Monitoring change data capture with Kinesis Data Streams<a name="kds_using-shards-and-metrics.monitoring"></a>

DynamoDB provides several Amazon CloudWatch metrics to help you monitor the replication of change data capture to Kinesis\. For a full list of CloudWatch metrics, see [DynamoDB Metrics and dimensions](metrics-dimensions.md)\.

To determine whether your stream has sufficient capacity, we recommend that you monitor the following items both during stream enabling and in production:
+ `ThrottledPutRecordCount`: The number of records that that were throttled by your Kinesis data stream because of insufficient Kinesis data stream capacity\. You might experience some throttling during exceptional usage peaks, but the `ThrottledPutRecordCount` should remain as low as possible\. DynamoDB retries sending throttled records to the Kinesis data stream, but this might result in higher replication latency\. 

  If you experience excessive and regular throttling, you might need to increase the number of Kinesis stream shards proportionally to the observed write throughput of your table\. To learn more about determining the size of a Kinesis data stream, see [Determining the Initial Size of a Kinesis Data Stream](https://docs.aws.amazon.com/streams/latest/dev/amazon-kinesis-streams.html#how-do-i-size-a-stream)\.
+ `AgeOfOldestUnreplicatedRecord`: The elapsed time since the oldest item\-level change yet to replicate to the Kinesis data stream appeared in the DynamoDB table\. Under normal operation, `AgeOfOldestUnreplicatedRecord` should be in the order of milliseconds\. This number grows based on unsuccessful replication attempts when these are caused by customer\-controlled configuration choices\.

  Customer\-controlled configuration examples that leads to unsuccessful replication attempts are an under\-provisioned Kinesis data stream capacity that leads to excessive throttling, or a manual update to your Kinesis data stream’s access policies that prevents DynamoDB from adding data to your data stream\. To keep this metric as low as possible, you might need to ensure the right provisioning of your Kinesis data stream capacity, and make sure that DynamoDB’s permissions are unchanged\. 
+ `FailedToReplicateRecordCount`: The number of records that DynamoDB failed to replicate to your Kinesis data stream\. Certain items larger than 34 KB might expand in size to change data records that are larger than the 1 MB item size limit of Kinesis Data Streams\. This size expansion occurs when these larger than 34 KB items include a large number of Boolean or empty attribute values\. Boolean and empty attribute values are stored as 1 byte in DynamoDB, but expand up to 5 bytes when they’re serialized using standard JSON for Kinesis Data Streams replication\. DynamoDB can’t replicate such change records to your Kinesis data stream\. DynamoDB skips these change data records, and automatically continues replicating subsequent records\. 

   

You can create Amazon CloudWatch alarms that send an Amazon Simple Notification Service \(Amazon SNS\) message for notification when any of the preceding metrics exceed a specific threshold\. For more information, see [Creating CloudWatch alarms to monitor DynamoDB](creating-alarms.md)\.