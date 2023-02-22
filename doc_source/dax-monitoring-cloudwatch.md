# Monitoring with Amazon CloudWatch<a name="dax-monitoring-cloudwatch"></a>

You can monitor Amazon DynamoDB Accelerator \(DAX\) using Amazon CloudWatch, which collects and processes raw data from DAX into readable, near real\-time metrics\. These statistics are recorded for a period of two weeks\. You can then access historical information for a better perspective on how your web application or service is performing\. By default, DAX metric data is sent to CloudWatch automatically\. For more information, see [What Is Amazon CloudWatch?](https://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/WhatIsCloudWatch.html) in the *Amazon CloudWatch User Guide*\.

**Topics**
+ [How do I use DAX metrics?](#dax-how-to-use-metrics)
+ [Viewing DAX metrics and dimensions](dax-metrics-dimensions-dax.md)
+ [Creating CloudWatch alarms to monitor DAX](dax-creating-alarms.md)

## How do I use DAX metrics?<a name="dax-how-to-use-metrics"></a>

The metrics reported by DAX provide information that you can analyze in different ways\. The following list shows some common uses for the metrics\. These are suggestions to get you started, and not a comprehensive list\.


****  

|  How Can I?  |  Relevant Metrics  | 
| --- | --- | 
|  Determine if any system errors occurred |  Monitor `FaultRequestCount` to determine if any requests resulted in an HTTP 500 \(server error\) code\. This can indicate a DAX internal service error or an HTTP 500 in the underlying table's [SystemErrors metric](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/metrics-dimensions.html)\.   | 
| Determine if any user errors occurred |  Monitor `ErrorRequestCount` to determine if any requests resulted in an HTTP 400 \(client error\) code\. If you see the error count growing, you might want to investigate and make sure you are sending correct client requests\.   | 
| Determine if any cache misses occurred |  Monitor `ItemCacheMisses` to determine the number of times an item was not found in the cache, and `QueryCacheMisses` and `ScanCacheMisses` to determine the number of times a query or scan result was not found in the cache\.   | 
| Monitor cache hit rates |  Use [CloudWatch Metric Math](http://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/using-metric-math.html) to define a cache hit rate metric using math expressions\. For example, for the item cache, you can use the expression m1/SUM\(\[m1, m2\]\)\*100, where m1 is the `ItemCacheHits` metric and m2 is the `ItemCacheMisses` metric for your cluster\. For the query and scan caches, you can follow the same pattern using the corresponding query and scan cache metric\.   | 