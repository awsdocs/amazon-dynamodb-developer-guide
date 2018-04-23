# Backing Up a DynamoDB Table<a name="Backup.Tutorial"></a>

This section describes how to use the Amazon DynamoDB console, or the AWS Command Line Interface to back up a table\.

**Topics**
+ [Creating a Table Backup \(Console\)](#backup_console)
+ [Creating a Table Backup \(AWS CLI\)](#backup_cli)

## Creating a Table Backup \(Console\)<a name="backup_console"></a>

Follow these steps to create a backup named `MusicCollectionBackup` for an existing `MusicCollection` table using the AWS Management Console\.

1. Sign in to the AWS Management Console and open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. You can create a backup by doing one of the following:
   + On the **Backups** tab of the `MusicCollection` table, choose **Create backup**\.
   + In the navigation pane on the left side of the console, choose **Backups**\. Then choose **Create backup**\.

1. Make sure that **MusicCollection** is the table name, and type **MusicCollectionBackup** for the backup name\. Then, choose **Create** to create the backup\.  
![\[Screenshot of create table backup screen with table name and backup name filled in.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/confirm_createbackup.png)
**Note**  
If you create backups using the **Backups** section in the navigation pane, the table isn't preselected for you\. You have to manually choose the source table name for the backup\.

   While the backup is being created, the backup status is set to **Creating**\. After the backup is finalized, the backup status changes to **Available**\.  
![\[Screenshot of the MusicCollectionBackup with status showing available.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/confirm_backup.png)

## Creating a Table Backup \(AWS CLI\)<a name="backup_cli"></a>

Follow these steps to create a backup for an existing table `MusicCollection` using the AWS CLI\.
+ Create a backup with the name `MusicCollectionBackup` for the `MusicCollection` table:

  ```
  aws dynamodb create-backup --table-name MusicCollection \
   --backup-name MusicCollectionBackup
  ```

   While the backup is being created, the backup status is set to `CREATING`:

  ```
  {
      "BackupDetails": {
          "BackupName": "MusicCollectionBackup", 
          "BackupArn": "arn:aws:dynamodb:us-east-1:123456789012:table/MusicCollection/backup/01489602797149-73d8d5bc", 
          "BackupStatus": "CREATING", 
          "BackupCreationDateTime": 1489602797.149
      }
  }
  ```

After the backup is finalized, its `BackupStatus` should change to `AVAILABLE`\. To confirm this, use the `describe-backup` command\. You can get the input value of `backup-arn` from the output of the previous step or by using the `list-backups` command\.

```
aws dynamodb describe-backup \
--backup-arn arn:aws:dynamodb:us-east-1:123456789012:table/MusicCollection/backup/01489173575360-b308cd7d
```

 To keep track of your backups, you can use the `list-backups` command\. It lists all your backups that are in `CREATING` or `AVAILABLE` status: 

```
aws dynamodb list-backups
```

 The `list-backups` command and the `describe-backup` command are useful to check information about the source table of the backup\.