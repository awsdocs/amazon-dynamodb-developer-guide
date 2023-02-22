# Monitoring with Amazon CloudWatch<a name="monitoring-cloudwatch"></a>

You can monitor Amazon DynamoDB using CloudWatch, which collects and processes raw data from DynamoDB into readable, near real\-time metrics\. These statistics are retained for a period of time, so that you can access historical information for a better perspective on how your web application or service is performing\. By default, DynamoDB metric data is sent to CloudWatch automatically\. For more information, see [What is Amazon CloudWatch?](https://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/WhatIsCloudWatch.html) and [Metrics retention](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch_concepts.html#metrics-retention) in the *Amazon CloudWatch User Guide*\.

**Topics**
+ [DynamoDB Metrics and dimensions](metrics-dimensions.md)
+ [How do I use DynamoDB metrics?](#how-to-use-metrics)
+ [Understanding and analyzing DynamoDB response times](#Understanding-Response-times)
+ [Creating CloudWatch alarms to monitor DynamoDB](creating-alarms.md)

## How do I use DynamoDB metrics?<a name="how-to-use-metrics"></a>

The metrics reported by DynamoDB provide information that you can analyze in different ways\. The following list shows some common uses for the metrics\. These are suggestions to get you started, not a comprehensive list\.


****  

|  How can I?  |  Relevant metrics  | 
| --- | --- | 
|  How can I monitor the rate of TTL deletions on my table?  |  You can monitor `TimeToLiveDeletedItemCount` over the specified time period, to track the rate of TTL deletions on your table\. For an example of a server\-less application using the `TimeToLiveDeletedItemCount` metric, see [ Automatically archive items to S3 using DynamoDB time to live \(TTL\) with AWS Lambda and Amazon Kinesis Data Firehose](https://aws.amazon.com/blogs/database/automatically-archive-items-to-s3-using-dynamodb-time-to-live-with-aws-lambda-and-amazon-kinesis-firehose/)\.  | 
|  How can I determine how much of my provisioned throughput is being used?  |  You can monitor `ConsumedReadCapacityUnits` or `ConsumedWriteCapacityUnits` over the specified time period, to track how much of your provisioned throughput is being used\.  | 
|  How can I determine which requests exceed the provisioned throughput quotas of a table?  |  `ThrottledRequests` is incremented by one if any event within a request exceeds a provisioned throughput quota\. Then, to gain insight into which event is throttling a request, compare `ThrottledRequests` with the `ReadThrottleEvents` and `WriteThrottleEvents` metrics for the table and its indexes\.  | 
|  How can I determine if any system errors occurred?  |  You can monitor `SystemErrors` to determine if any requests resulted in an HTTP 500 \(server error\) code\. Typically, this metric should be equal to zero\. If it isn't, then you might want to investigate\.  You might encounter internal server errors while working with items\. These are expected during the lifetime of a table\. Any failed requests can be retried immediately\.   | 

## Understanding and analyzing DynamoDB response times<a name="Understanding-Response-times"></a>

When analyzing latency, it's generally best to check the average\. Occasional spikes in latency aren't a cause for concern\. However, if average latency is high then an underlying issue could be responsible\. 

 There are two categories of latency, API latency and service side latency\. DynamoDB's API latency is measured from steps 1 through 11 in the process below\. Service\-side latency is measured from the moment an API reaches the Request Router \(step 4 \) to the time the RR takes to send a result back to the application user \(step 11\)\. You can analyze service side latency with the Amazon CloudWatch metric `SucessfulRequestLatency`\. 

When any application makes any DynamoDB API call to DynamoDB, such as issuing a `GetItem` operation to a DynamoDB table, the following steps occur\. 

1. The application resolves the DynamoDB public endpoint using local DNS server\.

1. The application connects to the IP address resolved in step one, and makes an API call\. 

1. The DynamoDB public endpoint takes the request and forwards it to a component called Request Router \(RR\)\. 

1. Once the API request reaches the Request Router \(RR\), RR does authentication and authorization of the API call\. RR also does the throttling checks at this stage\. 

1. After completing all the checks Request Router \(RR\) creates the hash of the partition key value which it gets from the API request\. Based upon hash value, Request Router \(RR\) finds the partition information \(storage node\) details\.

1. The Storage Nodes represent the servers where customer table data is stored\. A single partition \(not to be confused with primary or partition key\), consists of a set of 3 Storage Nodes\. Out of these three Storage Nodes, one node acts as a leader for that partition and the remaining two nodes acts as follower\. 

1. If the API call is a write request or if it is strongly consistent read request, then Request Routers \(RR\) finds the leader node for that partition and forwards the API request to that specific node\. In case of eventually consistent read request, Request Routers\(RR\) forwards the request to leader node or any of the follower nodes for that partition randomly\. 

1. In normal circumstances, the Request Router reaches the the Storage Node in first attempt\. If this attempt fails, RR does multiple retries to reach the storage node\. While connecting to storage node, RR always gives enough time to the existing attempt and then tries to connect to the different SN\. This is an internal micro\-service retry, and not a configurable SDK retry\. 

1. At this stage the API request reaches to Storage Node \(SN\) layer and SN starts processing it, reading or writing the data depending of the API call\. 

1. After successfully processing the API request, the Storage Node \(SN\) return the results or response code to the RR which originated the request\.

1. Finally, Request Router forwards the results to the customer application\. 

![\[DynamoDB API call steps\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/ddb-latency-steps.png)

**Note**  
For most atomic operations, such as `GetItem` and `PutItem`, you can expect an average latency in single\-digit milliseconds\. Latency for non\-atomic operations such as `Query` and `Scan` depends on many factors, including the size of the result set and the complexity of the query conditions and filters\.
DynamoDB doesn't measure the amount of time an application takes to connect with the DynamoDB public endpoint, or the amount of time an application takes to download the results from the public endpoint\.
