# Deleting a DynamoDB Table Backup<a name="Delete.Tutorial"></a>

This section describes how to use the AWS Management Console or the AWS Command Line Interface \(AWS CLI\) to delete an Amazon DynamoDB table backup\. 

**Note**  
If you want to use the AWS CLI, you have to configure it first\. For more information, see [Using the AWS CLI](Tools.CLI.md)\.

**Topics**
+ [Deleting a Table Backup \(Console\)](#deletebackup_console)
+ [Deleting a Table Backup \(AWS CLI\)](#deletebackup_cli)

## Deleting a Table Backup \(Console\)<a name="deletebackup_console"></a>

The following procedure shows how to use the console to delete the `MusicBackup` that is created in the [Backing Up a DynamoDB Table](Backup.Tutorial.md) tutorial\.

**To delete a backup**

1. Sign in to the AWS Management Console and open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. In the navigation pane on the left side of the console, choose **Backups**\.

1. In the list of backups, choose `MusicBackup`\.  
![\[Screenshot showing the MusicBackup with status as available.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/deleteBackup.png)![\[Screenshot showing the MusicBackup with status as available.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Screenshot showing the MusicBackup with status as available.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

1. Choose **Delete backup**:  
![\[Screenshot of the backups list with delete backup button highlighted.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/deleteBackupSelected.png)![\[Screenshot of the backups list with delete backup button highlighted.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Screenshot of the backups list with delete backup button highlighted.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

   Choose **Delete** to delete the backup\.  
![\[Screenshot of the delete backup screen.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/ConfirmDeleteBackup.png)![\[Screenshot of the delete backup screen.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Screenshot of the delete backup screen.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

## Deleting a Table Backup \(AWS CLI\)<a name="deletebackup_cli"></a>

The following example deletes a backup for an existing table `Music` table using the AWS CLI\.

```
aws dynamodb delete-backup \
--backup-arn arn:aws:dynamodb:us-east-1:123456789012:table/Music/backup/01489602797149-73d8d5bc
```