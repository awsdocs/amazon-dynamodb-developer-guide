# Backup and Restore: How It Works<a name="backuprestore_HowItWorks"></a>

This section provides an overview of what happens during the backup and restore process in Amazon DynamoDB\.

## Backups<a name="backuprestore_HowItWorks-backup"></a>

 When you create an on\-demand backup, a time marker of the request is cataloged\. The backup is created asynchronously by applying all changes until the time of the request to the last full table snapshot\. Backup requests are processed instantaneously and become available for restore within minutes\. 

**Note**  
 Each time you create an on\-demand backup, the entire table data is backed up\. There is no limit to the number of on\-demand backups that can be taken\.

All backups in DynamoDB work without consuming any provisioned throughput on the table\. 

DynamoDB backups do not guarantee causal consistency across items; however, the skew between updates in a backup is usually much less than a second\.

 Along with data, the following are also included \(and can't be excluded\) on the backups: 
+ Global secondary indexes \(GSIs\)
+ Local secondary indexes \(LSIs\)
+ Streams
+ Provisioned read and write capacity

Restored table items are consistent with LSI projections and eventually consistent with GSI projections\. 

You must manually set up the following on the restored table:
+ Auto scaling policies
+ AWS Identity and Access Management \(IAM\) policies
+ Amazon CloudWatch metrics and alarms
+ Tags
+ Stream settings
+ Time To Live \(TTL\) settings

While a backup is in progress, you can't do the following:
+ Pause or cancel the backup operation\.
+ Delete the source table of the backup\.
+ Disable backups on a table if a backup for that table is in progress\.

**Note**  
 Currently, backup and restore works only in the same AWS Region as the source table\. 

 You can schedule periodic or future backups by using AWS Lambda functions\. For more information, see [A serverless solution to schedule your Amazon DynamoDB On\-Demand Backup\.](https://aws.amazon.com/blogs/database/a-serverless-solution-to-schedule-your-amazon-dynamodb-on-demand-backup/) 

 If you don't want to create scheduling scripts and cleanup jobs, you can use AWS Backup to create backup plans with schedules and retention policies for your DynamoDB tables\. AWS Backup will execute the backups and delete them when they expire\. 

 If using the console, any backups created using AWS Backup will be listed on the **Backups** tab with the **Backup type** set to `AWS`\. 

**Note**  
 AWS backups cannot be deleted from the DynamoDB console\. You can use the AWS backup console to manage these backups\. 

To learn how to perform a backup, see [Backing Up a DynamoDB Table](Backup.Tutorial.md)\.

## Restores<a name="backuprestore_HowItWorks-restore"></a>

A table is restored without consuming any provisioned throughput on the table\. The destination table is set with the same provisioned read capacity units and write capacity units as the source table, as recorded at the time the backup was requested\. The restore process also restores the local secondary indexes and the global secondary indexes\. 

 You can only restore the entire table data to a new table from a backup\. You can write to the restored table only after it becomes active\. 

**Note**  
 You can't overwrite an existing table during a restore operation\. 

 The time it takes you to restore a table will vary based on multiple factors, and the restore times are not always correlated directly to the size of the table\. For example, because of parallelization, it is possible that restoring a 300 GB table could take the same amount of time as restoring a 3 GB table\. Here are some of the considerations for restore times: 
+  You restore backups to a new table\. It can take up to 20 minutes \(even if the table is empty\) to perform all the actions to create the new table and initiate the restore process\. 
+  For tables with even data distribution across your primary keys, the restore time is proportional to the largest single partition by item count and not the overall table size\. For the largest partitions with billions of items, a restore could take less than 10 hours\. 
+  If your source table contains data with significant skew, the time to restore may increase\. For example, if your table’s primary key is using the month of the year for partitioning and all your data is from the month of December, you have skewed data\. 

To learn how to perform a restore, see [Restoring a DynamoDB Table from a Backup](Restore.Tutorial.md)\. 

 You can use IAM policies for access control\. For more information, see [Using IAM with DynamoDB Backup and Restore](backuprestore_IAM.md)\. 

 All backup and restore console and API actions are captured and recorded in AWS CloudTrail for logging, continuous monitoring, and auditing\. 