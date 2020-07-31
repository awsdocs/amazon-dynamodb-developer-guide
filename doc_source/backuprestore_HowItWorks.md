# Back Up and Restore DynamoDB Tables: How It Works<a name="backuprestore_HowItWorks"></a>

You can use the on\-demand backup feature to create full backups of your Amazon DynamoDB tables\. This section provides an overview of what happens during the backup and restore process\.

## Backups<a name="backuprestore_HowItWorks-backup"></a>

When you create an on\-demand backup, a time marker of the request is cataloged\. The backup is created asynchronously by applying all changes until the time of the request to the last full table snapshot\. Backup requests are processed instantaneously and become available for restore within minutes\.

**Note**  
Each time you create an on\-demand backup, the entire table data is backed up\. There is no limit to the number of on\-demand backups that can be taken\.

All backups in DynamoDB work without consuming any provisioned throughput on the table\.

DynamoDB backups do not guarantee causal consistency across items; however, the skew between updates in a backup is usually much less than a second\.

While a backup is in progress, you can't do the following:
+ Pause or cancel the backup operation\.
+ Delete the source table of the backup\.
+ Disable backups on a table if a backup for that table is in progress\.

You can schedule periodic or future backups by using AWS Lambda functions\. For more information, see the blog post [A serverless solution to schedule your Amazon DynamoDB On\-Demand Backup](https://aws.amazon.com/blogs/database/a-serverless-solution-to-schedule-your-amazon-dynamodb-on-demand-backup/)\.

If you don't want to create scheduling scripts and cleanup jobs, you can use AWS Backup to create backup plans with schedules and retention policies for your DynamoDB tables\. AWS Backup executes the backups and deletes them when they expire\. For more information, see the [AWS Backup Developer Guide](https://docs.aws.amazon.com/aws-backup/latest/devguide/whatisbackup.html)\.

If you're using the console, any backups created using AWS Backup are listed on the **Backups** tab with the **Backup type** set to `AWS`\.

**Note**  
You can't delete backups marked with a **Backup type** of AWS using the DynamoDB console\. To manage these backups, use the AWS Backup console\.

To learn how to perform a backup, see [Backing Up a DynamoDB Table](Backup.Tutorial.md)\.

## Restores<a name="backuprestore_HowItWorks-restore"></a>

You restore a table without consuming any provisioned throughput on the table\. You can do a full table restore from your DynamoDB backup, or you can configure the destination table settings\. When you do a restore, you can change the following table settings:
+ Global secondary indexes \(GSIs\)
+ Local secondary indexes \(LSIs\)
+ Billing mode
+ Provisioned read and write capacity
+ Encryption settings

**Important**  
When you do a full table restore, the destination table is set with the same provisioned read capacity units and write capacity units as the source table, as recorded at the time the backup was requested\. The restore process also restores the local secondary indexes and the global secondary indexes\.

You can also restore your DynamoDB table data across AWS Regions such that the restored table is created in a different Region from where the backup resides\. You can do cross\-Region restores between AWS commercial Regions, AWS China Regions, and AWS GovCloud \(US\) Regions\. You pay only for the data that you transfer out of the source Region and for restoring to a new table in the destination Region\.

**Note**  
Cross\-Region restore is not supported if the source or destination Region is Asia Pacific \(Hong Kong\) or Middle East \(Bahrain\)\.

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

The time it takes you to restore a table varies based on multiple factors\. The restore times are not always correlated directly to the size of the table\. For example, because of parallelization, restoring a 300 GB table could take the same amount of time as restoring a 3 GB table\.

The following are some considerations for restore times:
+ You restore backups to a new table\. It can take up to 20 minutes \(even if the table is empty\) to perform all the actions to create the new table and initiate the restore process\.
+ For tables with even data distribution across your primary keys, the restore time is proportional to the largest single partition by item count and not the overall table size\. For the largest partitions with billions of items, a restore could take less than 10 hours\.
+ If your source table contains data with significant skew, the time to restore might increase\. For example, if your table’s primary key is using the month of the year for partitioning, and all your data is from the month of December, you have skewed data\.

To learn how to perform a restore, see [Restoring a DynamoDB Table from a Backup](Restore.Tutorial.md)\.

You can use IAM policies for access control\. For more information, see [Using IAM with DynamoDB Backup and Restore](backuprestore_IAM.md)\.

All backup and restore console and API actions are captured and recorded in AWS CloudTrail for logging, continuous monitoring, and auditing\.