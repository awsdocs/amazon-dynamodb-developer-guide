# Backing up and restoring DynamoDB tables with DynamoDB: How it works<a name="CreateBackup"></a>

You can use the DynamoDB on\-demand backup feature to create full backups of your Amazon DynamoDB tables\. This feature is available independently from AWS backup\. This section provides an overview of what happens during the DynamoDB backup and restore process\.

## Backups<a name="CreateBackup_HowItWorks"></a>

When you create an on\-demand backup with DynamoDB, a time marker of the request is cataloged\. The backup is created asynchronously by applying all changes until the time of the request to the last full table snapshot\. DynamoDB backup requests are processed instantaneously and become available for restore within minutes\.

**Note**  
Each time you create an on\-demand backup, the entire table data is backed up\. There is no limit to the number of on\-demand backups that can be taken\.

All backups in DynamoDB work without consuming any provisioned throughput on the table\.

DynamoDB backups do not guarantee causal consistency across items; however, the skew between updates in a backup is usually much less than a second\.

While a backup is in progress, you can't do the following:
+ Pause or cancel the backup operation\.
+ Delete the source table of the backup\.
+ Disable backups on a table if a backup for that table is in progress\.

If you don't want to create scheduling scripts and cleanup jobs, you can use AWS Backup to create backup plans with schedules and retention policies for your DynamoDB tables\. AWS Backup runs the backups and deletes them when they expire\. For more information, see the [AWS Backup Developer Guide](https://docs.aws.amazon.com/aws-backup/latest/devguide/whatisbackup.html)\.

In addition to AWS Backup, you can schedule periodic or future backups by using AWS Lambda functions\. For more information, see the blog post [A serverless solution to schedule your Amazon DynamoDB On\-Demand backup](https://aws.amazon.com/blogs/database/a-serverless-solution-to-schedule-your-amazon-dynamodb-on-demand-backup/)\.

If you're using the console, any backups created using AWS Backup are listed on the **Backups** tab with the **Backup type** set to `AWS`\.

**Note**  
You can't delete backups marked with a **Backup type** of AWS using the DynamoDB console\. To manage these backups, use the AWS Backup console\.

To learn how to perform a backup, see [Backing up a DynamoDB table](Backup.Tutorial.md)\.

## Restores<a name="CreateBackup_HowItWorks-restore"></a>

You restore a table without consuming any provisioned throughput on the table\. You can do a full table restore from your DynamoDB backup, or you can configure the destination table settings\. When you do a restore, you can change the following table settings:
+ Global secondary indexes \(GSIs\)
+ Local secondary indexes \(LSIs\)
+ Billing mode
+ Provisioned read and write capacity
+ Encryption settings

**Important**  
When you do a full table restore, the destination table is set with the same provisioned read capacity units and write capacity units as the source table, as recorded at the time the backup was requested\. The restore process also restores the local secondary indexes and the global secondary indexes\.

You can also restore your DynamoDB table data across AWS Regions such that the restored table is created in a different Region from where the backup resides\. You can do cross\-Region restores between AWS commercial Regions, AWS China Regions, and AWS GovCloud \(US\) Regions\. You pay only for the data that you transfer out of the source Region and for restoring to a new table in the destination Region\.

Restores can be faster and more cost\-efficient if you choose to exclude some or all secondary indexes from being created on the new restored table\.

You must manually set up the following on the restored table:
+ Auto scaling policies
+ AWS Identity and Access Management \(IAM\) policies
+ Amazon CloudWatch metrics and alarms
+ Tags
+ Stream settings
+ Time to Live \(TTL\) settings

You can only restore the entire table data to a new table from a backup\. You can write to the restored table only after it becomes active\.

**Note**  
 You can't overwrite an existing table during a restore operation\.

Service metrics show that 95 percent of customers' table restores complete in less than one hour\. However, restore times are directly related to the configuration of your tables \(such as the size of your tables and the number of underlying partitions\) and other related variables\. A best practice when planning for disaster recovery is to regularly document average restore completion times and establish how these times affect your overall Recovery Time Objective\.

To learn how to perform a restore, see [Restoring a DynamoDB table from a backup](Restore.Tutorial.md)\.

You can use IAM policies for access control\. For more information, see [Using IAM with DynamoDB backup and restore](backuprestore_IAM.md)\.

All backup and restore console and API actions are captured and recorded in AWS CloudTrail for logging, continuous monitoring, and auditing\.