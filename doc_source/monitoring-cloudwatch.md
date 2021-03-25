# Monitoring with Amazon CloudWatch<a name="monitoring-cloudwatch"></a>

You can monitor Amazon DynamoDB using CloudWatch, which collects and processes raw data from DynamoDB into readable, near real\-time metrics\. These statistics are retained for a period of time, so that you can access historical information for a better perspective on how your web application or service is performing\. By default, DynamoDB metric data is sent to CloudWatch automatically\. For more information, see [What Is Amazon CloudWatch?](https://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/WhatIsCloudWatch.html) and [Metrics Retention](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch_concepts.html#metrics-retention) in the *Amazon CloudWatch User Guide*\.

**Topics**
+ [DynamoDB Metrics and Dimensions](metrics-dimensions.md)
+ [How Do I Use DynamoDB Metrics?](#how-to-use-metrics)
+ [Creating CloudWatch Alarms to Monitor DynamoDB](creating-alarms.md)

## How Do I Use DynamoDB Metrics?<a name="how-to-use-metrics"></a>

The metrics reported by DynamoDB provide information that you can analyze in different ways\. The following list shows some common uses for the metrics\. These are suggestions to get you started, not a comprehensive list\.


****  

|  How can I?  |  Relevant Metrics  | 
| --- | --- | 
|  How can I monitor the rate of TTL deletions on my table?  |  You can monitor `TimeToLiveDeletedItemCount` over the specified time period, to track the rate of TTL deletions on your table\. For an example of a server\-less application using the `TimeToLiveDeletedItemCount` metric, see [ Automatically archive items to S3 using DynamoDB Time to Live \(TTL\) with AWS Lambda and Amazon Kinesis Data Firehose](https://aws.amazon.com/blogs/database/automatically-archive-items-to-s3-using-dynamodb-time-to-live-with-aws-lambda-and-amazon-kinesis-firehose/)\.  | 
|  How can I determine how much of my provisioned throughput is being used?  |  You can monitor `ConsumedReadCapacityUnits` or `ConsumedWriteCapacityUnits` over the specified time period, to track how much of your provisioned throughput is being used\.  | 
|  How can I determine which requests exceed the provisioned throughput quotas of a table?  |  `ThrottledRequests` is incremented by one if any event within a request exceeds a provisioned throughput quota\. Then, to gain insight into which event is throttling a request, compare `ThrottledRequests` with the `ReadThrottleEvents` and `WriteThrottleEvents` metrics for the table and its indexes\.  | 
|  How can I determine if any system errors occurred?  |  You can monitor `SystemErrors` to determine if any requests resulted in an HTTP 500 \(server error\) code\. Typically, this metric should be equal to zero\. If it isn't, then you might want to investigate\.  You might encounter internal server errors while working with items\. These are expected during the lifetime of a table\. Any failed requests can be retried immediately\.   | 