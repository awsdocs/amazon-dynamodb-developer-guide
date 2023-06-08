# DynamoDB data export to Amazon S3: how it works<a name="S3DataExport.HowItWorks"></a>

Using DynamoDB table export, you can export data from an Amazon DynamoDB table from any time within your point\-in\-time recovery window to an Amazon S3 bucket\. Exporting a DynamoDB table to an S3 bucket enables you to perform analytics and complex queries on your data using other AWS services such as Athena, AWS Glue, and Lake Formation\. DynamoDB table export is a fully managed solution for exporting DynamoDB tables at scale, and is much faster than other workarounds involving table scans\.

Exporting a table does not consume read capacity on the table, and has no impact on table performance and availability\. You can export table data to an S3 bucket owned by another AWS account, and to a different Region than the one your table is in\. Your data is always encrypted end\-to\-end\.

You can export a DynamoDB table using the AWS Management Console, the AWS Command Line Interface, or the DynamoDB API\. For more information, see [Requesting a table export in DynamoDB](S3DataExport_Requesting.md)\.

For more information about AWS Region availability and pricing, see [Amazon DynamoDB pricing](https://aws.amazon.com/dynamodb/pricing)\.

To export data from an Amazon DynamoDB table to an Amazon S3 bucket, point\-in\-time recovery \(PITR\) must be enabled on the source table\. You can export table data from any point in time within the PITR window, up to 35 days\. For more information, see [Point\-in\-time recovery for DynamoDB](PointInTimeRecovery.md)\.

Exporting a table does not consume read capacity on the table, and has no impact on table performance and availability\. You can export table data to an S3 bucket owned by another AWS account, and to a different Region than the one your table is in\. Your data is always encrypted at rest and in transit\.

You can choose to export your data in either DynamoDB JSON format or Amazon Ion text format\. For more information about export formats, see [Data objects](S3DataExport.Output.md#S3DataExport.Output_Data)\.

You can export data to an S3 bucket owned by a different account if you have the correct permissions to write to that bucket\. The destination bucket may be in a different region from the source table\. For more information, see [Amazon S3 setup and permissions](S3DataExport_Requesting.md#S3DataExport_Requesting_Permissions)\.

Up to 300 export tasks, or up to 100 TB of table size, can be exported concurrently\.

The request time and the time of the last update included in the data lake export request may vary within a one\-minute time window\. For example, if you submit the request at 2:25 PM, the output is guaranteed to contain all data committed to the table up to 2:24, and data committed after 2:26 will not be included\. The output may or may not contain data modifications made between 2:24 and 2:26\. Exported data is also not transactionally consistent\.

AWS CloudTrail logs all console and API actions for table export to enable logging, continuous monitoring, and auditing\. For more information, see [Logging DynamoDB operations by using AWS CloudTrail](logging-using-cloudtrail.md)\.

DynamoDB table export is designed to be faster than exporting a table using a table scan\. However, the exact time it takes for the export to complete depends on how large the table is and how uniformly the table data is distributed\. If your use case involves real\-time analytics, you can use Amazon Kinesis Data Streams\. For more information, see the [Amazon Kinesis Data Streams Developer Guide](https://docs.aws.amazon.com/streams/latest/dev/)\.

For more information about Amazon S3, see the [Amazon Simple Storage Service User Guide](https://docs.aws.amazon.com/AmazonS3/latest/dev/)\.

**Topics**
+ [Requesting a table export in DynamoDB](S3DataExport_Requesting.md)
+ [DynamoDB table export output format](S3DataExport.Output.md)
+ [Using DynamoDB table exports with other AWS services](S3DataExport.Using.md)