# Point\-in\-time recovery for DynamoDB<a name="PointInTimeRecovery"></a>

You can create on\-demand backups of your Amazon DynamoDB tables, or you can enable continuous backups using point\-in\-time recovery\. For more information about on\-demand backups, see [Using On\-Demand backup and restore for DynamoDB](BackupRestore.md)\.

Point\-in\-time recovery helps protect your DynamoDB tables from accidental write or delete operations\. With point\-in\-time recovery, you don't have to worry about creating, maintaining, or scheduling on\-demand backups\. For example, suppose that a test script writes accidentally to a production DynamoDB table\. With point\-in\-time recovery, you can restore that table to any point in time during the last 35 days\. DynamoDB maintains incremental backups of your table\.

In addition, point\-in\-time operations don't affect performance or API latencies\. For more information, see [Point\-in\-time recovery: How it works](PointInTimeRecovery_Howitworks.md)\. 

You can restore a DynamoDB table to a point in time using the AWS Management Console, the AWS Command Line Interface \(AWS CLI\), or the DynamoDB API\. The point\-in\-time recovery process restores to a new table\. For more information, see [Restoring a DynamoDB table to a point in time](PointInTimeRecovery.Tutorial.md)\.

For more information about AWS Region availability and pricing, see [Amazon DynamoDB pricing](https://aws.amazon.com/dynamodb/pricing)\.

**Topics**
+ [Point\-in\-time recovery: How it works](PointInTimeRecovery_Howitworks.md)
+ [Before you begin using point\-in\-time recovery](pointintimerecovery_beforeyoubegin.md)
+ [Restoring a DynamoDB table to a point in time](PointInTimeRecovery.Tutorial.md)