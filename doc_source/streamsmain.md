# Change data capture with Amazon DynamoDB<a name="streamsmain"></a>

Many applications benefit from capturing changes to items stored in a DynamoDB table, at the point in time when such changes occur\. The following are some example use cases:
+ A popular mobile app modifies data in a DynamoDB table, at the rate of thousands of updates per second\. Another application captures and stores data about these updates, providing near\-real\-time usage metrics for the mobile app\.
+ A financial application modifies stock market data in a DynamoDB table\. Different applications running in parallel track these changes in real time, compute value\-at\-risk, and automatically rebalance portfolios based on stock price movements\.
+ Sensors in transportation vehicles and industrial equipment send data to a DynamoDB table\. Different applications monitor performance and send messaging alerts when a problem is detected, predict any potential defects by applying machine learning algorithms, and compress and archive data to Amazon Simple Storage Service \(Amazon S3\)\.
+ An application automatically sends notifications to the mobile devices of all friends in a group as soon as one friend uploads a new picture\.
+ A new customer adds data to a DynamoDB table\. This event invokes another application that sends a welcome email to the new customer\.

DynamoDB supports streaming of item\-level change data capture records in near\-real time\. You can build applications that consume these streams and take action based on the contents\.

The following video will give you an introductory look at the change data capture concept\.

[![AWS Videos](http://img.youtube.com/vi/https://www.youtube.com/embed/VVv_-mZ5Ge8/0.jpg)](http://www.youtube.com/watch?v=https://www.youtube.com/embed/VVv_-mZ5Ge8)

**Topics**
+ [Streaming options for change data capture](#streamsmain.choose)
+ [Using Kinesis Data Streams to capture changes to DynamoDB](kds.md)
+ [Change data capture for DynamoDB Streams](Streams.md)

## Streaming options for change data capture<a name="streamsmain.choose"></a>

DynamoDB offers two streaming models for change data capture: Kinesis Data Streams for DynamoDB and DynamoDB Streams\.

To help you choose the right solution for your application, the following table summarizes the features of each streaming model\. 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/streamsmain.html)

You can enable both streaming models on the same DynamoDB table\. 