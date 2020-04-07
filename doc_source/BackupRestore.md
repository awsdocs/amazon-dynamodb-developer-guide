# On\-Demand Backup and Restore for DynamoDB<a name="BackupRestore"></a>

You can create on\-demand backups for your Amazon DynamoDB tables or enable continuous backups with point\-in\-time recovery\. For information about continuous backups with point\-in\-time recovery, see [Point\-in\-Time Recovery for DynamoDB](PointInTimeRecovery.md)\.

You can use the DynamoDB on\-demand backup capability to create full backups of your tables for long\-term retention and archival for regulatory compliance needs\. You can back up and restore your table data anytime with a single click on the AWS Management Console or with a single API call\. Backup and restore actions execute with zero impact on table performance or availability\.

The on\-demand backup and restore process scales without degrading the performance or availability of your applications\. It uses a new and unique distributed technology that lets you complete backups in seconds regardless of table size\. You can create backups that are consistent within seconds across thousands of partitions without worrying about schedules or long\-running backup processes\. All backups are cataloged, easily discoverable, and retained until explicitly deleted\.

In addition, on\-demand backup and restore operations don't affect performance or API latencies\. Backups are preserved regardless of table deletion\. For more information, see [Back Up and Restore DynamoDB Tables: How It Works](backuprestore_HowItWorks.md)\.

You can create table backups using the console, the AWS Command Line Interface \(AWS CLI\), or the DynamoDB API\. For more information, see [Backing Up a DynamoDB Table](Backup.Tutorial.md)\.

For information about restoring a table from a backup, see [Restoring a DynamoDB Table from a Backup](Restore.Tutorial.md)\.

DynamoDB on\-demand backups are available at no additional cost beyond the normal pricing that's associated with backup storage size\. For more information about AWS Region availability and pricing, see [Amazon DynamoDB pricing](https://aws.amazon.com/dynamodb/pricing)\.

**Topics**
+ [Back Up and Restore DynamoDB Tables: How It Works](backuprestore_HowItWorks.md)
+ [Backing Up a DynamoDB Table](Backup.Tutorial.md)
+ [Restoring a DynamoDB Table from a Backup](Restore.Tutorial.md)
+ [Deleting a DynamoDB Table Backup](Delete.Tutorial.md)
+ [Using IAM with DynamoDB Backup and Restore](backuprestore_IAM.md)