# Evaluate your Streams usage<a name="CostOptimization_StreamsUsage"></a>

This section provides an overview of how to evaluate your DynamoDB Streams usage\. There are certain usage patterns which are not optimal for DynamoDB, and they allow room for optimization from both a performance and cost perspective\.

You have two native streaming integrations for streaming and event\-driven use cases:
+ [Amazon DynamoDB Streams](Streams.md) 
+ [Amazon Kinesis Data Streams](Streams.KCLAdapter.md) 

This page will just focus on cost optimization strategies for these options\. If you'd like to instead find out how to choose between the two options, see [Streaming options for change data capture](streamsmain.md#streamsmain.choose)\.

**Topics**
+ [Optimizing costs for DynamoDB Streams](#CostOptimization_StreamsUsage_Options_DDBStreams)
+ [Optimizing costs for Kinesis Data Streams](#CostOptimization_StreamsUsage_Options_KDS)
+ [Cost optimization strategies for both types of Streams usage](#CostOptimization_StreamsUsage_GuidanceForBoth)

## Optimizing costs for DynamoDB Streams<a name="CostOptimization_StreamsUsage_Options_DDBStreams"></a>

As mentioned in the [pricing page](http://aws.amazon.com/dynamodb/pricing/on-demand/) for DynamoDB Streams, regardless of the table’s throughput capacity mode, DynamoDB charges on the number of read requests made towards the table’s DynamoDB Stream\. Read requests made towards a DynamoDB Stream are different from the read requests made towards a DynamoDB table\.

Each read request in terms of the stream is in the form of a `GetRecords` API call that can return up to 1000 records or 1 MB worth of records in the response, whichever is reached first\. None of the [other DynamoDB Stream APIs](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Operations_Amazon_DynamoDB_Streams.html) are charged and DynamoDB Streams are not charged for being idle\. In other words, if no read requests are made to a DynamoDB Stream, no charges will be incurred for having a DynamoDB Stream enabled on a table\.

Here are a few consumer applications for DynamoDB Streams:
+ AWS Lambda function\(s\)
+ Amazon Kinesis Data Streams\-based applications
+ Customer consumer applications built using an AWS SDK

Read requests made by AWS Lambda\-based consumers of DynamoDB Streams are free, whereas calls made by consumers of any other kind are charged\. Every month, the first 2,500,000 read requests made by non\-Lambda consumers are also free of cost\. This applies to all read requests made to any DynamoDB Streams in an AWS Account for each AWS Region\.

**Monitoring your DynamoDB Streams usage**  
DynamoDB Streams charges on the billing console are grouped together for all DynamoDB Streams across the AWS Region in an AWS Account\. Currently, tagging DynamoDB Streams is not supported, so cost allocation tags cannot be used to identify granular costs for DynamoDB Streams\. The volume of `GetRecords` calls can be obtained at the DynamoDB Stream level to compute the charges per stream\. The volume is represented by the DynamoDB Stream’s CloudWatch metric `SuccessfulRequestLatency` and its `SampleCount` statistic\. This metric will also include `GetRecords` calls made by global tables to perform on\-going replication as well as calls made by AWS Lambda consumers, both of which are not charged\. For information on other CloudWatch metrics published by DynamoDB Streams, see [DynamoDB Metrics and dimensions](metrics-dimensions.md)\.

**Using AWS Lambda as the consumer**  
Evaluate if using AWS Lambda functions as the consumers for DynamoDB Streams is feasible because that can eliminate costs associated with reading from the DynamoDB Stream\. On the other hand, DynamoDB Streams Kinesis Adapter or SDK based consumer applications will be charged on the number of `GetRecords` calls they make towards the DynamoDB Stream\.

Lambda function invocations will be charged based on standard Lambda pricing, however no charges will be incurred by DynamoDB Streams\. Lambda will poll shards in your DynamoDB Stream for records at a base rate of 4 times per second\. When records are available, Lambda invokes your function and waits for the result\. If processing succeeds, Lambda resumes polling until it receives more records\.

**Tuning DynamoDB Streams Kinesis Adapter\-based consumer applications**  
Since read requests made by non\-Lambda based consumers are charged for DynamoDB Streams, it is important to find a balance between the near real\-time requirement and the number of times the consumer application must poll the DynamoDB Stream\. 

The frequency of polling DynamoDB Streams using a DynamoDB Streams Kinesis Adapter based application is determined by the configured `idleTimeBetweenReadsInMillis` value\. This parameter determines the amount of time in milliseconds that the consumer should wait before processing a shard in case the previous `GetRecords` call made to the same shard did not return any records\. By default, this value for this parameter is 1000 ms\. If near real\-time processing is not required, this parameter could be increased to have the consumer application make fewer `GetRecords` calls and optimize on DynamoDB Streams calls\.

## Optimizing costs for Kinesis Data Streams<a name="CostOptimization_StreamsUsage_Options_KDS"></a>

When a Kinesis Data Stream is set as the destination to deliver change data capture events for a DynamoDB table, the Kinesis Data Stream may need separate sizing management which will affect the overall costs\. DynamoDB charges in terms of Change Data capture Units \(CDUs\) where each unit is a made of up a 1 KB DynamoDB item size attempted by the DynamoDB service to the destination Kinesis Data Stream\.

In addition to charges by the DynamoDB service, standard Kinesis Data Stream charges will be incurred\. As mentioned in the [pricing page](http://aws.amazon.com/kinesis/data-streams/pricing/), the service pricing differs based on the capacity mode \- provisioned and on\-demand, which are distinct from DynamoDB table capacity modes and are user\-defined\. At a high level, Kinesis Data Streams charges an hourly rate based on the capacity mode, as well as on data ingested into the stream by DynamoDB service\. There may be additional charges like data retrieval \(for on\-demand mode\), extended data retention \(beyond default 24 hours\), and enhanced fan\-out consumer retrievals depending on the user configuration for the Kinesis Data Stream\.

**Monitoring your Kinesis Data Streams usage**  
Kinesis Data Streams for DynamoDB publishes metrics from DynamoDB in addition to standard Kinesis Data Stream CloudWatch Metrics\. It may be possible that a `Put` attempt by the DynamoDB service is throttled by the Kinesis service because of insufficient Kinesis Data Streams capacity, or by dependent components like a AWS KMS service that may be configured to encrypt the Kinesis Data Stream data at rest\.

To learn more about CloudWatch metrics published by DynamoDB service for the Kinesis Data Stream, see [Monitoring change data capture with Kinesis Data Streams](kds_using-shards-and-metrics.md#kds_using-shards-and-metrics.monitoring)\. In order to avoid additional costs of service retries due to throttles, it is important to right size the Kinesis Data Stream in case of Provisioned Mode\.

**Choosing the right capacity mode for Kinesis Data Streams**  
Kinesis Data Streams are supported in two capacity modes – provisioned mode and on\-demand mode\.
+ If the workload involving Kinesis Data Stream has predictable application traffic, traffic that is consistent or ramps gradually, or traffic that can be forecasted accurately, then Kinesis Data Streams’ **provisioned mode** is suitable and will be more cost efficient
+ If the workload is new, has unpredictable application traffic, or you prefer not to manage capacity, then Kinesis Data Streams’ **on\-demand mode** is suitable and will be more cost efficient

A best practice to optimize costs would be to evaluate if the DynamoDB table associated with the Kinesis Data Stream has a predictable traffic pattern that can leverage provisioned mode of Kinesis Data Streams\. If the workload is new, you could use on\-demand mode for the Kinesis Data Streams for a few initial weeks, review the CloudWatch metrics to understand traffic patterns, and then switch the same Stream to provisioned mode based on the nature of the workload\. In the case of provisioned mode, estimation on number shards can be made by following shard management considerations for Kinesis Data Streams\.

**Evaluate your consumer applications using Kinesis Data Streams for DynamoDB**  
Since Kinesis Data Streams don’t charge on the number of `GetRecords` calls like DynamoDB Streams, consumer applications could make as many number of calls as possible, provided the frequency is under the throttling limits for `GetRecords`\. In terms of on\-demand mode for Kinesis Data Streams, data reads are charged on a per GB basis\. For provisioned mode Kinesis Data Streams, reads are not charged if the data is less than 7 days old\. In the case of Lambda functions as Kinesis Data Streams consumers, Lambda polls each shard in your Kinesis Stream for records at a base rate of once per second\.

## Cost optimization strategies for both types of Streams usage<a name="CostOptimization_StreamsUsage_GuidanceForBoth"></a>

**Event filtering for AWS Lambda consumers**  
Lambda event filtering allows you to discard events based on a filter criteria from being available in the Lambda function invocation batch\. This optimizes Lambda costs for processing or discarding unwanted stream records within the consumer function logic\. To learn more about configuring event filtering and writing your filtering criteria, see [Lambda event filtering](https://docs.aws.amazon.com/lambda/latest/dg/invocation-eventfiltering.html)\.

**Tuning AWS Lambda consumers**  
Costs could be further be optimized by tuning Lambda configuration parameters like increasing the `BatchSize` to process more per invocation, enabling `BisectBatchOnFunctionError` to prevent processing duplicates \(which incurs additional costs\), and setting `MaximumRetryAttempts` to not run into too many retries\. By default, failed consumer Lambda invocations are retried infinitely until the record expires from the stream, which is around 24 hours for DynamoDB Streams and configurable from 24 hours to up to 1 year for Kinesis Data Streams\. Additional Lambda configuration options available including the ones mentioned above for DynamoDB Stream consumers are in the [AWS Lambda developer guide](https://docs.aws.amazon.com/lambda/latest/dg/with-ddb.html#services-ddb-params)\.