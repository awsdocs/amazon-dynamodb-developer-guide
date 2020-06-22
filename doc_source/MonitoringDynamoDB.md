# Logging and Monitoring in DynamoDB<a name="MonitoringDynamoDB"></a>

Monitoring is an important part of maintaining the reliability, availability, and performance of DynamoDB and your AWS solutions\. You should collect monitoring data from all parts of your AWS solution so that you can more easily debug a multi\-point failure, if one occurs\. Before you start monitoring DynamoDB, you should create a monitoring plan that includes answers to the following questions:
+ What are your monitoring goals?
+ What resources will you monitor?
+ How often will you monitor these resources?
+ What monitoring tools will you use?
+ Who will perform the monitoring tasks?
+ Who should be notified when something goes wrong?

The next step is to establish a baseline for normal DynamoDB performance in your environment, by measuring performance at various times and under different load conditions\. As you monitor DynamoDB, you should consider storing historical monitoring data\. This stored data will give you a baseline from which to compare current performance data, identify normal performance patterns and performance anomalies, and devise methods to address issues\. 

To establish a baseline you should, at a minimum, monitor the following items:
+ The number of read or write capacity units consumed over the specified time period, so you can track how much of your provisioned throughput is used\.
+ Requests that exceeded a table's provisioned write or read capacity during the specified time period, so you can determine which requests exceed the provisioned throughput quotas of a table\.
+ System errors, so you can determine if any requests resulted in an error\.

**Topics**
+ [Monitoring Tools](monitoring-automated-manual.md)
+ [Monitoring with Amazon CloudWatch](monitoring-cloudwatch.md)
+ [Logging DynamoDB Operations by Using AWS CloudTrail](logging-using-cloudtrail.md)