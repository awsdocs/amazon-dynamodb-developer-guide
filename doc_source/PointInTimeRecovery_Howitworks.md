# Point\-in\-time recovery: How it works<a name="PointInTimeRecovery_Howitworks"></a>

Amazon DynamoDB point\-in\-time recovery \(PITR\) provides automatic backups of your DynamoDB table data\. This section provides an overview of how the process works in DynamoDB\.

## Enabling point\-in\-time recovery<a name="howitworks_enabling"></a>

You can enable point\-in\-time recovery using the AWS Management Console, AWS Command Line Interface \(AWS CLI\), or the DynamoDB API\. When enabled, point\-in\-time recovery provides continuous backups until you explicitly turn it off\. For more information, see [Restoring a DynamoDB table to a point in time](PointInTimeRecovery.Tutorial.md)\.

After you enable point\-in\-time recovery, you can restore to any point in time within `EarliestRestorableDateTime` and `LatestRestorableDateTime`\. `LatestRestorableDateTime` is typically 5 minutes before the current time\.

**Note**  
The point\-in\-time recovery process always restores to a new table\.

## Restoring a table using point\-in\-time recovery<a name="howitworks_restoring"></a>

For `EarliestRestorableDateTime`, you can restore your table to any point in time during the last 35 days\. The retention period is a fixed 35 days \(5 calendar weeks\) and can't be modified\. Any number of users can run up to four concurrent restores \(any type of restore\) in a given account\.

**Important**  
If you disable point\-in\-time recovery and later re\-enable it on a table, you reset the start time for which you can recover that table\. As a result, you can only immediately restore that table using the `LatestRestorableDateTime`\.

When you restore using point\-in\-time recovery, DynamoDB restores your table data to the state based on the selected date and time \(`day:hour:minute:second`\) to a new table\.

You restore a table without consuming any provisioned throughput on the table\. You can do a full table restore using point\-in\-time recovery, or you can configure the destination table settings\. You can change the following table settings on the restored table:
+ Global secondary indexes \(GSIs\)
+ Local secondary indexes \(LSIs\)
+ Billing mode
+ Provisioned read and write capacity
+ Encryption settings

**Important**  
When you do a full table restore, the destination table is set with the same provisioned read capacity units and write capacity units that the source table had when the backup was requested\. For example, suppose that a table's provisioned throughput was recently lowered to 50 read capacity units and 50 write capacity units\. You then restore the table's state to three weeks ago, at which time its provisioned throughput was set to 100 read capacity units and 100 write capacity units\. In this case, DynamoDB restores your table data to that point in time with the provisioned throughput from that time \(100 read capacity units and 100 write capacity units\)\.

You can also restore your DynamoDB table data across AWS Regions such that the restored table is created in a different Region from where the source table resides\. You can do cross\-Region restores between AWS commercial Regions, AWS China Regions, and AWS GovCloud \(US\) Regions\. You pay only for the data you transfer out of the source Region and for restoring to a new table in the destination Region\.

**Note**  
Cross\-Region restore is not supported if the source or destination Region is Asia Pacific \(Hong Kong\) or Middle East \(Bahrain\)\.

Restores can be faster and more cost\-efficient if you exclude some or all indexes from being created on the restored table\.

You must manually set the following on the restored table:
+ Auto scaling policies
+ AWS Identity and Access Management \(IAM\) policies
+ Amazon CloudWatch metrics and alarms
+ Tags
+ Stream settings
+ Time to Live \(TTL\) settings
+ Point\-in\-time recovery settings

The time it takes you to restore a table varies based on multiple factors\. The point\-in\-time restore times are not always correlated directly to the size of the table\. For more information, see [Restores](CreateBackup.md#CreateBackup_HowItWorks-restore)\.

## Deleting a table with point\-in\-time recovery enabled<a name="howitworks_deleting"></a>

When you delete a table that has point\-in\-time recovery enabled, DynamoDB automatically creates a backup snapshot called a *system backup* and retains it for 35 days \(at no additional cost\)\. You can use the system backup to restore the deleted table to the state it was in just before deletion\. All system backups follow a standard naming convention of *table\-name*`$DeletedTableBackup`\.

**Note**  
Once a table with point\-in\-time recovery enabled is deleted, you can use system restore to restore that table to a single point in time: the moment just before deletion\. You do not have the capability to restore a deleted table to any other point in time in the past 35 days\.