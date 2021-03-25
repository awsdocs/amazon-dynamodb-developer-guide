# Monitoring DAX<a name="DAX.Monitoring"></a>

Monitoring is an important part of maintaining the reliability, availability, and performance of Amazon DynamoDB Accelerator \(DAX\) and your AWS solutions\. You should collect monitoring data from all parts of your AWS solution so that you can more easily debug a multi\-point failure, if one occurs\.

Before you start monitoring DAX, you should create a monitoring plan that includes answers to the following questions:
+ What are your monitoring goals?
+ What resources will you monitor?
+ How often will you monitor these resources?
+ What monitoring tools will you use?
+ Who will perform the monitoring tasks?
+ Who should be notified when something goes wrong?

The next step is to establish a baseline for normal DAX performance in your environment, by measuring performance at various times and under different load conditions\. As you monitor DAX, you should consider storing historical monitoring data\. This stored data gives you a baseline from which to compare current performance data, identify normal performance patterns and performance anomalies, and devise methods to address issues\. 

To establish a baseline, you should, at a minimum, monitor the following items both during load testing and in production:
+ CPU utilization and throttled requests, so that you can determine whether you might need to use a larger node type in your cluster\. The CPU utilization of your cluster is available through the `CPUUtilization` [CloudWatch metric](dax-metrics-dimensions-dax.md)\.
+ Operation latency \(as measured on the client side\) should remain consistently within your application's latency requirements\.
+ Error rates should remain low, as seen from the `ErrorRequestCount`, `FaultRequestCount`, and `FailedRequestCount` [CloudWatch metrics](dax-metrics-dimensions-dax.md)\.

Apart from the preceding items, you should, at a minimum, monitor the following additional items in production:
+ Estimated database size and evicted size, so that you can determine whether the cluster’s node type has sufficient memory to hold your working set\.
+ Client connections, so that you can monitor for any unexplained spikes in connections to the cluster\.

**Topics**
+ [Monitoring Tools](dax-monitoring-automated-manual.md)
+ [Monitoring with Amazon CloudWatch](dax-monitoring-cloudwatch.md)
+ [Logging DAX Operations Using AWS CloudTrail](dax-logging-using-cloudtrail.md)