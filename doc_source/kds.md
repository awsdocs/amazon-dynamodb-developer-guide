# Using Kinesis Data Streams to Capture Changes to DynamoDB<a name="kds"></a>

You can use Amazon Kinesis Data Streams to capture changes to Amazon DynamoDB\.

Kinesis Data Streams captures item\-level modifications in any DynamoDB table and replicates them to a [Kinesis data stream](https://docs.aws.amazon.com/streams/latest/dev/introduction.html)\. Your applications can access this stream and view item\-level changes in near\-real time\. You can continuously capture and store terabytes of data per hour\. You can take advantage of longer data retention time—and with enhanced fan\-out capability, you can simultaneously reach two or more downstream applications\. Other benefits include additional audit and security transparency\.

Kinesis Data Streams also gives you access to [Amazon Kinesis Data Firehose](https://docs.aws.amazon.com/firehose/latest/dev/what-is-this-service.html) and [Amazon Kinesis Data Analytics](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/what-is.html)\. These services can help you build applications that power real\-time dashboards, generate alerts, implement dynamic pricing and advertising, and implement sophisticated data analytics and machine learning algorithms\.

**Note**  
Using Kinesis data streams for DynamoDB is subject to both [Kinesis Data Streams pricing](https://aws.amazon.com/kinesis/data-streams/pricing/) for the data stream and [DynamoDB pricing](https://aws.amazon.com/dynamodb/pricing/) for the source table\.

## How Kinesis Data Streams Works with DynamoDB<a name="kds_howitworks"></a>

When a Kinesis data stream is enabled for a DynamoDB table, the table sends out a data record that captures any changes to that table’s data\. This data record includes:
+ The specific time any item was recently created, updated, or deleted
+ That item’s primary key
+ An image of the item before the modification
+ An image of the item after the modification 

These data records are captured and published in near\-real time\. After they are written to the Kinesis data stream, they can be read just like any other record\. You can use the Kinesis Client Library, use AWS Lambda, call the Kinesis Data Streams API, and use other connected services\. For more information, see [Reading Data from Amazon Kinesis Data Streams](https://docs.aws.amazon.com/streams/latest/dev/building-consumers.html) in the Amazon Kinesis Data Streams Developer Guide\.

These changes to data are also captured asynchronously\. Kinesis has no performance impact on a table that it’s streaming from\. The stream records stored in your Kinesis data stream are also encrypted at rest\. For more information, see [Data Protection in Amazon Kinesis Data Streams](https://docs.aws.amazon.com/streams/latest/dev/server-side-encryption.html)\.

The Kinesis data stream records might appear in a different sequence than the item changes occurred\. The same item notifications might also appear more than once in the stream\. You can check the `ApproximateCreationDateTime` attribute to identify the actual order that the item modifications occurred, and to identify duplicate records\. 

`ApproximateCreationDateTime` indicates the time of the modification in milliseconds\. If more than one modification occurred to the same item within the same millisecond, the `ApproximateCreationDateTime` is increased by 1 millisecond to present a useful order of occurrence\.

DynamoDB charges for using Kinesis Data Streams in change data capture units\. 1 KB of change per single item counts as one change data capture unit\. The KB of change in each item is calculated by the larger of the “before” and “after” images of the item written to the stream, using the same logic as [capacity unit consumption for write operations](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ProvisionedThroughput.html#ItemSizeCalculations.Writes)\. Similar to how DynamoDB [on\-demand](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.ReadWriteCapacityMode.html#HowItWorks.OnDemand) mode works, you don't need to provision capacity throughput for change data capture units\.

**Topics**
+ [Turning on a Kinesis Data Stream for Your DynamoDB Table](#kds_howitworks.enabling)

### Turning on a Kinesis Data Stream for Your DynamoDB Table<a name="kds_howitworks.enabling"></a>

You can enable or disable streaming to Kinesis from your existing DynamoDB table by using the AWS Management Console, the AWS SDK, or the AWS Command Line Interface \(AWS CLI\)\.
+ You can only stream data from DynamoDB to Kinesis Data Streams in the same AWS account and AWS Region as your table\. 
+ You can only stream data from a DynamoDB table to one Kinesis data stream\.

  