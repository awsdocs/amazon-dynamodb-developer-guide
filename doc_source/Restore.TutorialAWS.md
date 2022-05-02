# Restoring a Backup of a DynamoDB table from AWS Backup<a name="Restore.TutorialAWS"></a>

This section describes how to restore a backup of a DynamoDB table from AWS Backup\.

** [Restoring a DynamoDB Table from AWS Backup](#Restore.TutorialAWS.simple) [Restoring a DynamoDB Table to Another Region or Account](#Restore.TutorialAWS.another)**
+ [Restoring a DynamoDB Table from AWS Backup](#Restore.TutorialAWS.simple)
+ [Restoring a DynamoDB Table to Another Region or Account](#Restore.TutorialAWS.another)

## Restoring a DynamoDB Table from AWS Backup<a name="Restore.TutorialAWS.simple"></a>

To restore your DynamoDB tables from AWS Backup, follow these steps:

1. Sign in to the AWS Management Console and open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)

1. In the navigation pane on the left side of the console, choose **Tables\.**

1. Choose the **Backups** tab\.

1. Select the check box next to the previous backup that you want to restore from\.

1. Choose **Restore**\. You will be taken to the **Restore table from backup** screen\.

1. Enter the name for the newly restored table, the encryption that this new table will have, the key you want the restore to be encryped with, and other options\.

1. When you're finished, choose **Restore\.**

1. You will see a notification that the request to restore was submitted successfully\. To view the progress of restoring the table, choose **View details**\.

   This will take you to the AWS Backup console for further information on the progress of the task\.

## Restoring a DynamoDB Table to Another Region or Account<a name="Restore.TutorialAWS.another"></a>

To restore a DynamoDB table to another Region or account, you will first need to copy the backup to that new Region or account\. In order to copy to another account, that account must first grant you permission\. After you have copied your DynamoDB backup to the new Region or account, it can be restored with the process in the previous section\.