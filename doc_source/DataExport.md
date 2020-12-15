# Exporting DynamoDB table data to Amazon S3<a name="DataExport"></a>

Using DynamoDB table export, you can export data from an Amazon DynamoDB table from any time within your point\-in\-time recovery window to an Amazon S3 bucket\. Exporting a DynamoDB table to an S3 bucket enables you to perform analytics and complex queries on your data using other AWS services such as Athena, AWS Glue, and Lake Formation\. DynamoDB table export is a fully managed solution for exporting DynamoDB tables at scale, and is much faster than other workarounds involving table scans\. For more information, see [DynamoDB data export to Amazon S3: how it works](DataExport.HowItWorks.md)\.

Exporting a table does not consume read capacity on the table, and has no impact on table performance and availability\. You can export table data to an S3 bucket owned by another AWS account, and to a different region than the one your table is in\. Your data is always encrypted end\-to\-end\.

You can export a DynamoDB table using the AWS Management Console, the AWS Command Line Interface, or the DynamoDB API\. For more information, see [Requesting a table export in DynamoDB](DataExport.Requesting.md)\.

For more information about AWS Region availability and pricing, see [Amazon DynamoDB pricing](https://aws.amazon.com/dynamodb/pricing)\.

**Topics**
+ [DynamoDB data export to Amazon S3: how it works](DataExport.HowItWorks.md)
+ [Requesting a table export in DynamoDB](DataExport.Requesting.md)
+ [Table export output in DynamoDB](DataExport.Output.md)
+ [Using DynamoDB table exports with other AWS services](DataExport.Using.md)