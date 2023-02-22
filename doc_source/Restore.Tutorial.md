# Restoring a DynamoDB table from a backup<a name="Restore.Tutorial"></a>

This section describes how to restore a table from a backup using the Amazon DynamoDB console or the AWS Command Line Interface \(AWS CLI\)\. 

**Note**  
If you want to use the AWS CLI, you must configure it first\. For more information, see [Accessing DynamoDB](AccessingDynamoDB.md)\.

**Topics**
+ [Restoring a table from a backup \(console\)](#restoretable_console)
+ [Restoring a table from a backup \(AWS CLI\)](#restoretable_cli)

## Restoring a table from a backup \(console\)<a name="restoretable_console"></a>

The following procedure shows how to restore the `Music` table by using the `MusicBackup` file that is created in the [Backing up a DynamoDB table](Backup.Tutorial.md) tutorial\. 

**Note**  
This procedure assumes that the `Music` table no longer exists before restoring it using the `MusicBackup` file\.

**To restore a table from a backup**

1. Sign in to the AWS Management Console and open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. In the navigation pane on the left side of the console, choose **Backups**\.

1. In the list of backups, choose `MusicBackup`\.  
![\[Screenshot of the backups list and the create backup button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/select_musicbackup.png)![\[Screenshot of the backups list and the create backup button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Screenshot of the backups list and the create backup button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

1. Choose **Restore**\.

1. Enter **Music** as the new table name\. Confirm the backup name and other backup details\. Then choose **Restore table** to start the restore process\.
**Note**  
You can restore the table to the same AWS Region or to a different Region from where the backup resides\. You can also exclude secondary indexes from being created on the new restored table\. In addition, you can specify a different encryption mode\.  
Tables restored from backups are always created using the DynamoDB Standard table class\.  
![\[Screenshot of the restore table from backup screen with backup table details.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/restore_table.png)![\[Screenshot of the restore table from backup screen with backup table details.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Screenshot of the restore table from backup screen with backup table details.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

   The table that is being restored is shown with the status **Creating**\. After the restore process is finished, the status of the `Music` table changes to **Active**\.

## Restoring a table from a backup \(AWS CLI\)<a name="restoretable_cli"></a>

Follow these steps to use the AWS CLI to restore the `Music` table using the `MusicBackup` that is created in the [Backing up a DynamoDB table](Backup.Tutorial.md) tutorial\.

**To restore a table from a backup**

1. Confirm the backup that you want to restore by using the `list-backups` command\. This example uses `MusicBackup`\.

   ```
   aws dynamodb list-backups
   ```

   To get additional details for the backup, use the `describe-backup` command\. You can get the input `backup-arn` from the previous step\.

   ```
   aws dynamodb describe-backup \
   --backup-arn arn:aws:dynamodb:us-east-1:123456789012:table/Music/backup/01489173575360-b308cd7d
   ```

1. Restore the table from the backup\. In this case, the `MusicBackup` restores the `Music` table to the same AWS Region\.

   ```
   aws dynamodb restore-table-from-backup \
   --target-table-name Music \
   --backup-arn arn:aws:dynamodb:us-east-1:123456789012:table/Music/backup/01489173575360-b308cd7d
   ```

1. Restore the table from the backup with custom table settings\. In this case, the `MusicBackup` restores the `Music` table and specifies an encryption mode for the restored table\.
**Note**  
The `sse-specification-override` parameter takes the same values as the `sse-specification-override` parameter used in the `CreateTable` command\. To learn more, see [Managing encrypted tables in DynamoDB](encryption.tutorial.md)\.

   ```
   aws dynamodb restore-table-from-backup \
   --target-table-name Music \
   --backup-arn arn:aws:dynamodb:us-east-1:123456789012:table/Music/backup/01581080476474-e177ebe2 \
   --sse-specification-override Enabled=true,SSEType=KMS,KMSMasterKeyId=abcd1234-abcd-1234-a123-ab1234a1b234
   ```

   You can restore the table to a different AWS Region from where the backup resides\.
**Note**  
The `sse-specification-override` parameter is mandatory for cross\-Region restores but optional for restores in the same Region as the source table\.
When performing a cross\-Region restore from the command line, you must set the default AWS Region to the desired destination Region\. To learn more, see [Command line options](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-options.html) in the *AWS Command Line Interface User Guide*\.

   ```
   aws dynamodb restore-table-from-backup \
   --target-table-name Music \
   --backup-arn arn:aws:dynamodb:us-east-1:123456789012:table/Music/backup/01581080476474-e177ebe2 \
   --sse-specification-override Enabled=true,SSEType=KMS
   ```

   You can override the billing mode and the provisioned throughput for the restored table\.

   ```
   aws dynamodb restore-table-from-backup \
   --target-table-name Music \
   --backup-arn arn:aws:dynamodb:us-east-1:123456789012:table/Music/backup/01489173575360-b308cd7d \
   --billing-mode-override PAY_PER_REQUEST
   ```

   You can exclude some or all secondary indexes from being created on the restored table\.
**Note**  
Restores can be faster and more cost\-efficient if you exclude some or all secondary indexes from being created on the restored table\.

   ```
   aws dynamodb restore-table-from-backup \
   --target-table-name Music \
   --backup-arn arn:aws:dynamodb:us-east-1:123456789012:table/Music/backup/01581081403719-db9c1f91 \
   --global-secondary-index-override '[]' \
   --sse-specification-override Enabled=true,SSEType=KMS
   ```
**Note**  
The secondary indexes provided should match existing indexes\. You cannot create new indexes at the time of restore\.

   You can use a combination of different overrides\. For example, you can use a single global secondary index and change provisioned throughput at the same time, as follows\.

   ```
   aws dynamodb restore-table-from-backup \
   --target-table-name Music \
   --backup-arn arn:aws:dynamodb:eu-west-1:123456789012:table/Music/backup/01581082594992-303b6239 \
   --billing-mode-override PROVISIONED \
   --provisioned-throughput-override ReadCapacityUnits=100,WriteCapacityUnits=100 \
   --global-secondary-index-override IndexName=singers-index,KeySchema=["{AttributeName=SingerName,KeyType=HASH}"],Projection="{ProjectionType=KEYS_ONLY}",ProvisionedThroughput="{ReadCapacityUnits=5,WriteCapacityUnits=5}" \
   --sse-specification-override Enabled=true,SSEType=KMS
   ```

 To verify the restore, use the `describe-table` command to describe the `Music` table\.

```
aws dynamodb describe-table --table-name Music 
```

The table that is being restored from the backup is shown with the status **Creating**\. After the restore process is finished, the status of the `Music` table changes to **Active**\.

**Important**  
While a restore is in progress, don't modify or delete your IAM role policy; otherwise, unexpected behavior can result\. For example, suppose that you removed write permissions for a table while that table is being restored\. In this case, the underlying `RestoreTableFromBackup` operation would not be able to write any of the restored data to the table\.  
After the restore operation is complete, you can modify or delete your IAM role policy\.  
IAM policies involving [source IP restrictions](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourceip) for accessing the target restore table should have the [https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-viaawsservice](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-viaawsservice) key set to `false` to ensure that the restrictions apply only to requests made directly by a principal\. Otherwise, the restore will be canceled\.  
If your backup is encrypted with an AWS managed key or a customer managed key, don't disable or delete the key while a restore is in progress, or the restore will fail\.  
After the restore operation is complete, you can change the encryption key for the restored table and disable or delete the old key\.