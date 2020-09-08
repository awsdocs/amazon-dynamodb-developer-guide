# Viewing DAX Metrics and Dimensions<a name="dax-metrics-dimensions-dax"></a>

When you interact with Amazon DynamoDB, it sends metrics and dimensions to Amazon CloudWatch\. You can use the following procedures to view the metrics for DynamoDB Accelerator \(DAX\)\.

**To view metrics \(console\)**

Metrics are grouped first by the service namespace, and then by the various dimension combinations within each namespace\.

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Metrics**\.

1. Select the **DAX** namespace\.

**To view metrics \(AWS CLI\)**
+ At a command prompt, use the following command\.

  ```
  1. aws cloudwatch list-metrics --namespace "AWS/DAX"
  ```

## DAX Metrics and Dimensions<a name="dax-metrics-dimensions"></a>

The following sections contain the metrics and dimensions that DAX sends to CloudWatch\.

### DAX Metrics<a name="dax-metrics"></a>

The following metrics are available from DAX\. DAX sends metrics to CloudWatch only when they have a non\-zero value\.

**Note**  
CloudWatch aggregates the following DAX metrics at one\-minute intervals:  
`CPUUtilization`
`CacheMemoryUtilization`
`NetworkBytesIn`
`NetworkBytesOut`
`NetworkPacketsIn`
`NetworkPacketsOut`
`GetItemRequestCount`
`BatchGetItemRequestCount`
`BatchWriteItemRequestCount`
`DeleteItemRequestCount`
`PutItemRequestCount`
`UpdateItemRequestCount`
`TransactWriteItemsCount`
`TransactGetItemsCount`
`ItemCacheHits`
`ItemCacheMisses`
`QueryCacheHits`
`QueryCacheMisses`
`ScanCacheHits`
`ScanCacheMisses`
`TotalRequestCount`
`ErrorRequestCount`
`FaultRequestCount`
`FailedRequestCount`
`QueryRequestCount`
`ScanRequestCount`
`ClientConnections`
`EstimatedDbSize`
`EvictedSize`

Not all statistics, such as `Average` or `Sum`, are applicable for every metric\. However, all of these values are available through the DAX console, or by using the CloudWatch console, AWS CLI, or AWS SDKs for all metrics\. In the following table, each metric has a list of valid statistics that are applicable to that metric\.


****  

| Metric | Description | 
| --- | --- | 
| CPUUtilization |  The percentage of CPU utilization of the node or cluster\. Units: `Percent` Valid Statistics: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/dax-metrics-dimensions-dax.html)  | 
| CacheMemoryUtilization |  The percentage of available cache memory that is in use by the item cache and query cache on the node or cluster\. Cached data starts to be evicted prior to memory utilization reaching 100% \(see `EvictedSize` metric\)\. If `CacheMemoryUtilization` reaches 100% on any node, write requests will be throttled and you should consider switching to a cluster with a larger node type\. Units: `Percent` Valid Statistics: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/dax-metrics-dimensions-dax.html)  | 
| NetworkBytesIn |  The number of bytes received on all network interfaces by the node or cluster\. Units: `Bytes` Valid Statistics: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/dax-metrics-dimensions-dax.html)  | 
| NetworkBytesOut |  The number of bytes sent out on all network interfaces by the node or cluster\. This metric identifies the volume of outgoing traffic in terms of the number of bytes on a single node or cluster\. Units: `Bytes` Valid Statistics: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/dax-metrics-dimensions-dax.html)  | 
| NetworkPacketsIn |  The number of packets received on all network interfaces by the node or cluster\. Units: `Count` Valid Statistics: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/dax-metrics-dimensions-dax.html)  | 
| NetworkPacketsOut |  The number of packets sent out on all network interfaces by the node or cluster\. This metric identifies the volume of outgoing traffic in terms of the number of packets on a single node or cluster\. Units: `Count` Valid Statistics:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/dax-metrics-dimensions-dax.html)  | 
| GetItemRequestCount |  The number of `GetItem` requests handled by the node or cluster\. Units: `Count` Valid Statistics: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/dax-metrics-dimensions-dax.html)  | 
| BatchGetItemRequestCount |  The number of `BatchGetItem` requests handled by the node or cluster\. Units: `Count` Valid Statistics: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/dax-metrics-dimensions-dax.html)  | 
| BatchWriteItemRequestCount |  The number of `BatchWriteItem` requests handled by the node or cluster\.  Units: `Count` Valid Statistics: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/dax-metrics-dimensions-dax.html)  | 
| DeleteItemRequestCount |  The number of `DeleteItem` requests handled by the node or cluster\.  Units: `Count` Valid Statistics: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/dax-metrics-dimensions-dax.html)  | 
| PutItemRequestCount |  The number of `PutItem` requests handled by the node or cluster\. Units: `Count` Valid Statistics: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/dax-metrics-dimensions-dax.html)  | 
| UpdateItemRequestCount |  The number of `UpdateItem` requests handled by the node or cluster\.  Units: `Count` Valid Statistics: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/dax-metrics-dimensions-dax.html)  | 
| TransactWriteItemsCount |  The number of `TransactWriteItems` requests handled by the node or cluster\. Units: `Count` Valid Statistics: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/dax-metrics-dimensions-dax.html)  | 
| TransactGetItemsCount |  The number of `TransactGetItems` requests handled by the node or cluster\.  Units: `Count` Valid Statistics: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/dax-metrics-dimensions-dax.html)  | 
| ItemCacheHits |  The number of times an item was returned from the cache by the node or cluster\.  Units: `Count` Valid Statistics: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/dax-metrics-dimensions-dax.html)  | 
| ItemCacheMisses |  The number of times an item was not in the node or cluster cache, and had to be retrieved from DynamoDB\.  Units: `Count` Valid Statistics: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/dax-metrics-dimensions-dax.html)  | 
| QueryCacheHits |  The number of times a query result was returned from the node or cluster cache\.  Units: `Count` Valid Statistics: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/dax-metrics-dimensions-dax.html)  | 
| QueryCacheMisses |  The number of times a query result was not in the node or cluster cache, and had to be retrieved from DynamoDB\. Units: `Count` Valid Statistics: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/dax-metrics-dimensions-dax.html)  | 
| ScanCacheHits |  The number of times a scan result was returned from the node or cluster cache\.  Units: `Count` Valid Statistics: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/dax-metrics-dimensions-dax.html)  | 
| ScanCacheMisses |  The number of times a scan result was not in the node or cluster cache, and had to be retrieved from DynamoDB\. Units: `Count` Valid Statistics: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/dax-metrics-dimensions-dax.html)  | 
| TotalRequestCount |  Total number of requests handled by the node or cluster\.  Units: `Count` Valid Statistics: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/dax-metrics-dimensions-dax.html)  | 
| ErrorRequestCount |  Total number of requests that resulted in a user error reported by the node or cluster\. Requests that were throttled by the node or cluster are included\. Units: `Count` Valid Statistics: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/dax-metrics-dimensions-dax.html)  | 
| ThrottledRequestCount |  Total number of requests throttled by the node or cluster\. Requests that were throttled by DynamoDB are not included, and can be monitored using [DynamoDB Metrics](metrics-dimensions.md)\. Units: `Count` Valid Statistics: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/dax-metrics-dimensions-dax.html)  | 
| FaultRequestCount |  Total number of requests that resulted in an internal error reported by the node or cluster\.  Units: `Count` Valid Statistics: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/dax-metrics-dimensions-dax.html)  | 
| FailedRequestCount |  Total number of requests that resulted in an error reported by the node or cluster\.  Units: `Count` Valid Statistics: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/dax-metrics-dimensions-dax.html)  | 
| QueryRequestCount |  The number of query requests handled by the node or cluster\.  Units: `Count` Valid Statistics: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/dax-metrics-dimensions-dax.html)  | 
| ScanRequestCount |  The number of scan requests handled by the node or cluster\.  Units: `Count` Valid Statistics: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/dax-metrics-dimensions-dax.html)  | 
| ClientConnections |  The number of simultaneous connections made by clients to the node or cluster\.  Units: `Count` Valid Statistics: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/dax-metrics-dimensions-dax.html)  | 
| EstimatedDbSize |  An approximation of the amount of data cached in the item cache and the query cache by the node or cluster\.  Units: `Bytes` Valid Statistics: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/dax-metrics-dimensions-dax.html)  | 
| EvictedSize |  The amount of data that was evicted by the node or cluster to make room for newly requested data\. If the miss rate goes up, and you see this metric also growing, it probably means that your working set has increased\. You should consider switching to a cluster with a larger node type\. Units: `Bytes` Valid Statistics: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/dax-metrics-dimensions-dax.html)  | 

### Dimensions for DAX Metrics<a name="dax-metric-dimensions"></a>

The metrics for DAX are qualified by the values for the account, cluster ID, or cluster ID and node ID combination\. You can use the CloudWatch console to retrieve DAX data along any of the dimensions in the following table\. 


****  

|  Dimension  |  CloudWatch Metric Namespace  |  Description  | 
| --- | --- | --- | 
|  Account  |  DAX Metrics  |  Provides aggregated statistics across all nodes in an account\.  | 
|  ClusterId  |  Cluster Metrics  |  Limits the data to a cluster\.   | 
|  ClusterId, NodeId  |  ClusterId, NodeId  |  Limits the data to a node within a cluster\.   | 