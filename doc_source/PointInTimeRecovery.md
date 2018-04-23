# Point\-in\-Time Recovery for DynamoDB<a name="PointInTimeRecovery"></a>

 You can enable point\-in\-time recovery as well as create on\-demand backups for your Amazon DynamoDB tables\. For more information regarding on\-demand backups, see [On\-Demand Backup and Restore for DynamoDB](BackupRestore.md) 

 Point\-in\-time recovery helps protect your Amazon DynamoDB tables from accidental write or delete operations\. With point in time recovery, you don't have to worry about creating, maintaining, or scheduling on\-demand backups\. For example, suppose that a test script writes accidentally to a production DynamoDB table\. With point\-in\-time recovery, you can restore that table to any point in time during the last 35 days\. DynamoDB maintains incremental backups of your table\. 

In addition, point\-in\-time operations don't affect performance or API latencies\. For more information, see [Point\-in\-Time Recovery: How It Works](PointInTimeRecovery_Howitworks.md)\. 

You can restore a DynamoDB table to a point in time using the console, the AWS CLI, or the DynamoDB API\. For more information, see [Restoring a DynamoDB Table to a Point in Time](PointInTimeRecovery.Tutorial.md)\.

For more information about the AWS Region availability and pricing, see [Amazon DynamoDB Pricing](https://aws.amazon.com/dynamodb/pricing)\. 