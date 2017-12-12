# Restoring a DynamoDB Table from a Backup<a name="Restore.Tutorial"></a>

This section describes how to restore a table from a backup using the DynamoDB console or the AWS Command Line Interface\. \(If you want to use the AWS CLI, you need to configure it first\. For more information, see [Accessing DynamoDB](AccessingDynamoDB.md)\.\)


+ [Restoring a Table from a Backup \(Console\)](#restoretable_console)
+ [Restoring a Table from a Backup \(AWS CLI\)](#restoretable_cli)

## Restoring a Table from a Backup \(Console\)<a name="restoretable_console"></a>

The following procedure shows how to restore the `MusicCollection` table by using the `MusicCollectionBackup` file that is created in the [Backing Up a DynamoDB Table](Backup.Tutorial.md) tutorial\.

1. Sign in to the AWS Management Console and open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. In the navigation pane on the left side of the console, choose **Backups**\.

1. In the list of backups, choose `MusicCollectionBackup`\.  
![\[Screenshot of the backups list and the create backup
                            button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/select_musicbackup.png)

1. Choose **Restore backup**\.  
![\[Screenshot of the backups list with the restore backup button
                            highlighted.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/choose_restore.png)

1. Type **MusicCollection** as the new table name\. Confirm the backup name and other backup details\. Then choose **Restore table** to start the restore process\.  
![\[Screenshot of the restore table from backup screen with backup table
                            details.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/restore_table.png)

   The table that is being restored is shown with the status **Creating**\. After the restore process is finished, the status of the `MusicCollection` table changes to **Active**\.  
![\[Screenshot of the tables list with Active status.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/restore_complete.png)

## Restoring a Table from a Backup \(AWS CLI\)<a name="restoretable_cli"></a>

Follow these steps to use the AWS CLI to restore the `MusicCollection` table using the `MusicCollectionBackup` that is created in the [Backing Up a DynamoDB Table](Backup.Tutorial.md) tutorial\.

1. Confirm the backup that you want to restore by using the `list-backups` command\. This example uses `MusicCollectionBackup`\.

   ```
   aws dynamodb list-backups
   ```

   To get additional details for the backup, use the `describe-backup` command\. You can get the input `backup-arn` from the previous step: 

   ```
   aws dynamodb describe-backup \
   --backup-arn arn:aws:dynamodb:us-east-1:123456789012:table/MusicCollection/backup/01489173575360-b308cd7d
   ```

1. Restore the table from the backup\. In this case, the `MusicCollectionBackup` restores the `MusicCollection` table: 

   ```
   aws dynamodb restore-table-from-backup \
   --target-table-name MusicCollection \
   --backup-arn arn:aws:dynamodb:us-east-1:123456789012:table/MusicCollection/backup/01489173575360-b308cd7d
   ```

 To verify the restore, use the `describe-table` command to describe the `MusicCollection` table: 

```
aws dynamodb describe-table --table-name MusicCollection 
```

The table that is being restored from the backup is shown with the status **Creating**\. After the restore process is finished, the status of the `MusicCollection` table changes to **Active**\.

**Important**  
While a restore is in progress, do not modify or delete your IAM role policy; otherwise, unexpected behavior can result\. For example, suppose that you removed write permissions for a table while that table is being restored\. In this case, the underlying `RestoreTableFromBackup` operation would not be able to write any of the restored data to the table\.   
After the restore operation is complete, you can modify or delete your IAM role policy\.