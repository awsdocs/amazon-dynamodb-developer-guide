# Backup and Restore: How It Works<a name="backuprestore_HowItWorks"></a>

This section provides an overview of what happens during the backup and restore process in Amazon DynamoDB\.

## Backups<a name="backuprestore_HowItWorks-backup"></a>

 When you create an on\-demand backup, a time marker of the request is cataloged\. The backup is created asynchronously by applying all changes until the time of the request to the last full table snapshot\. Backup requests are processed instantaneously and become available for restore within minutes\. 

**Note**  
 Each time you create an on\-demand backup, the entire table data is backed up\. There is no limit to the number of on\-demand backups that can be taken\.

All backups in DynamoDB work without consuming any provisioned throughput on the table\. 

DynamoDB backups do not guarantee causal consistency across items; however, the skew between updates in a backup is usually much less than a second\.

 Along with data, the following are also included on the backups: 

+ Global secondary indexes \(GSIs\)

+ Local secondary indexes \(LSIs\)

+ Streams

+ Provisioned read and write capacity

For DynamoDB tables that have GSIs, the GSI data is automatically restored at table restore time\.

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

To learn how to perform a backup, see [Backing Up a DynamoDB Table](Backup.Tutorial.md)\.

## Restores<a name="backuprestore_HowItWorks-restore"></a>

A table is restored without consuming any provisioned throughput on the table\. The destination table is set with the same provisioned read capacity units and write capacity units as the source table, as recorded at the time the backup was requested\. The restore process also restores the local secondary indexes and the global secondary indexes\. 

 You can only restore the entire table data to a new table from a backup\. Restore times vary based on the size of the DynamoDB table that's being restored\. You can write to the restored table only after it becomes active\. 

**Note**  
 You can't overwrite an existing table during a restore operation\. 

To learn how to perform a restore, see [Restoring a DynamoDB Table from a Backup](Restore.Tutorial.md)\. 

 You can use IAM policies for access control\. For more information, see [Using IAM with DynamoDB Backup and Restore](backuprestore_IAM.md)\. 

 You can schedule periodic or future backups by using AWS Lambda functions\. Also, all backup and restore console and API actions are captured and recorded in AWS CloudTrail for logging, continuous monitoring, and auditing\. 