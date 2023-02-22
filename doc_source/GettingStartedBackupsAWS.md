# Backing up and restoring DynamoDB tables with AWS Backup: How it works<a name="GettingStartedBackupsAWS"></a>

You can use the on\-demand backup feature to create full backups of your Amazon DynamoDB tables\. This section provides an overview of what happens during the backup and restore process\.

## Backups<a name="GettingStartedBackupsAWS.backups"></a>

When you create an on\-demand backup with AWS Backup, a time marker of the request is cataloged\. The backup is created asynchronously by applying all changes until the time of the request to the last full table snapshot\.

Each time you create an on\-demand backup, the entire table data is backed up\. There is no limit to the number of on\-demand backups that can be taken\.

**Note**  
Unlike DynamoDB Backups, backups made with AWS Backup are not instantaneous\.

While a backup is in progress, you can't do the following:
+ Pause or cancel the backup operation\.
+ Delete the source table of the backup\.
+ Disable backups on a table if a backup for that table is in progress\.

AWS Backup provides automated backup schedules, retention management, and lifecycle management\. This removes the need for custom scripts and manual processes\. AWS Backup runs the backups and deletes them when they expire\. For more information, see the [AWS Backup Developer Guide](https://docs.aws.amazon.com/aws-backup/latest/devguide/whatisbackup.html)\.

If you're using the console, any backups created using AWS Backup are listed on the **Backups** tab with the **Backup type** set to `AWS_BACKUP`\.

**Note**  
You can't delete backups marked with a **Backup type** of `AWS_BACKUP` using the DynamoDB console\. To manage these backups, use the AWS Backup console\.

To learn how to perform a backup, see [Backing up a DynamoDB table](Backup.Tutorial.md)\.

## Restores<a name="GettingStartedBackupsAWS-restore"></a>

You restore a table without consuming any provisioned throughput on the table\. You can do a full table restore from your DynamoDB backup, or you can configure the destination table settings\. When you do a restore, you can change the following table settings:
+ Global secondary indexes \(GSIs\)
+ Local secondary indexes \(LSIs\)
+ Billing mode
+ Provisioned read and write capacity
+ Encryption settings

**Important**  
When you do a full table restore, the destination table is set with the same provisioned read capacity units and write capacity units that the source table had when the backup was requested\. The restore process also restores the local secondary indexes and the global secondary indexes\.

You can copy a backup of your DynamoDB table data to a different AWS Region and then restore it in that new Region\. You can copy and then restore backups between AWS commercial Regions, AWS China Regions, and AWS GovCloud \(US\) Regions\. You pay only for the data you copy from the source Region and the data you restore to a new table in the destination Region\.

AWS Backup will restore the tables with all the original indexes\.

You must manually set up the following on the restored table:
+ Auto scaling policies
+ AWS Identity and Access Management \(IAM\) policies
+ Amazon CloudWatch metrics and alarms
+ Tags
+ Stream settings
+ Time to Live \(TTL\) settings

You can only restore the entire table data to a new table from a backup\. You can write to the restored table only after it becomes active\.

**Note**  
AWS Backup restores are nondestructive\. You can't overwrite an existing table during a restore operation\.

Service metrics show that 95 percent of customers' table restores complete in less than one hour\. However, restore times are directly related to the configuration of your tables \(such as the size of your tables and the number of underlying partitions\) and other related variables\. A best practice when planning for disaster recovery is to regularly document average restore completion times and establish how these times affect your overall Recovery Time Objective\.

To learn how to perform a restore, see [Restoring a DynamoDB table from a backup](Restore.Tutorial.md)\.

You can use IAM policies for access control\. For more information, see [Using IAM with DynamoDB backup and restore](backuprestore_IAM.md)\.

All backup and restore console and API actions are captured and recorded in AWS CloudTrail for logging, continuous monitoring, and auditing\.