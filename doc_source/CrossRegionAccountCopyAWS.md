# Copying a Backup of a DynamoDB table with AWS Backup<a name="CrossRegionAccountCopyAWS"></a>

You can make a copy of a current backup\. You can copy backups to multiple AWS accounts or AWS Regions on demand or automatically as part of a scheduled backup plan\. You can also automate a sequence of cross\-account and cross\-Region copies for Amazon DynamoDB Encryption Client\.

Cross\-Region replication is especially valuable if you have business continuity or compliance requirements to store backups a minimum distance away from your production data\.

Cross\-account backups are useful for securely copying your backups to one or more AWS accounts in your organization for operational or security reasons\. If your original backup is inadvertently deleted, you can copy the backup from its destination account to its source account, and then start the restore\. Before you can do this, you must have two accounts that belong to the same organization in the Organizations service\.

Copies inherit the source backup's configuration unless you specify otherwise, with one exception: if you specify that your new copy "Never" expire\. With this setting, the new copy still inherits its source expiration date\. If you want your new backup copy to be permanent, either set your source backups to never expire, or specify your new copy to expire 100 years after its creation\.

**Note**  
If you're copying to another account, you must first have permission from that account\.

To copy a backup, do the following:

1. Sign in to the AWS Management Console and open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. In the navigation pane on the left side of the console, choose **Backups**\.

1. Select the check box next to the backup you want to copy\.
   + If the backup you want to copy is grayed out, you must enable [advanced features with AWS Backup](https://docs.aws.amazon.com/aws-backup/latest/devguide/advanced-ddb-backup.html)\. Then create a new backup\. You can now copy this new backup to other Regions and accounts, and copy any other new backups going forward\.

1.  Choose **Copy\.**

1. If you want to copy the backup to another account or Region, select the check box next to **Copy the recovery point to another destination**\. Then select whether you will to copy to another Region in your account, or to a different account in a different Region\.
**Note**  
To restore a backup to another Region or account, you must first copy the backup to that Region or account\.

1. Select the desired vault the file will be copied into\. You can also create a new backup vault if desired\.

1. Choose **Copy backup**\.