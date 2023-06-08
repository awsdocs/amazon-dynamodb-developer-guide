# Using AWS Backup with DynamoDB<a name="backuprestore_HowItWorksAWS"></a>

Amazon DynamoDB can help you meet regulatory compliance and business continuity requirements through enhanced backup features in AWS Backup\. AWS Backup is a fully managed data protection service that makes it easy to centralize and automate backups across AWS services, in the cloud, and on premises\. Using this service, you can configure backup policies and monitor activity for your AWS resources in one place\. To use AWS Backup, you must affirmatively [opt\-in](https://docs.aws.amazon.com/aws-backup/latest/devguide/service-opt-in.html)\. Opt\-in choices apply to the specific account and AWS Region, so you might have to opt in to multiple Regions using the same account\. For more information, see the [AWS Backup Developer Guide](https://docs.aws.amazon.com/aws-backup/latest/devguide/whatisbackup.html)\. 

Amazon DynamoDB is natively integrated with AWS Backup\. You can use AWS Backup to schedule, copy, tag and life cycle your DynamoDB on\-demand backups automatically\. You can continue to view and restore these backups from the DynamoDB console\. You can use the DynamoDB console, API, and AWS Command Line Interface \(AWS CLI\) to enable automatic backups for your DynamoDB tables\.

**Note**  
Any backups made through DynamoDB will remain unchanged\. You will still be able to create backups through the current DynamoDB workflow\.

Enhanced backup features available through AWS Backup include:

**Scheduled backups** \- You can set up regularly scheduled backups of your DynamoDB tables using backup plans\.

**Cross\-account and cross\-Region copying** \- You can automatically copy your backups to another backup vault in a different AWS Region or account, which allows you to support your data protection requirements\.

**Cold storage tiering** \- You can configure your backups to implement life cycle rules to delete or transition backups to colder storage\. This can help you optimize your backup costs\.

**Tags** \- You can automatically tag your backups for billing and cost allocation purposes\.

**Encryption** – DynamoDB on\-demand backups managed through AWS Backup are now stored in the AWS Backup vault\. This allows you to encrypt and secure your backups by using an AWS KMS key that is independent from your DynamoDB table encryption key\.

**Audit backups** – You can use AWS Backup Audit Manager to audit the compliance of your AWS Backup policies and to find backup activity and resources that are not yet compliant with the controls that you defined\. You can also use it to automatically generate an audit trail of daily and on\-demand reports for your backup governance purposes\. 

**Secure backups using the WORM model ** – You can use AWS Backup Vault Lock to enable a write\-once\-read\-many \(WORM\) setting for your backups\. With AWS Backup Vault Lock, you can add an additional layer of defense that protects backups from inadvertent or malicious delete operations, changes to backup retention periods, and updates to lifecycle settings\. To learn more, see [AWS Backup Vault Lock](https://docs.aws.amazon.com/aws-backup/latest/devguide/vault-lock.html)\.

These enhanced backup features are available in all AWS Regions\. To learn more about these features, see the [AWS Backup Developer Guide](https://docs.aws.amazon.com/aws-backup/latest/devguide/whatisbackup.html)\.

**Topics**
+ [Backing up and restoring DynamoDB tables with AWS Backup: How it works](GettingStartedBackupsAWS.md)
+ [Creating backups of DynamoDB tables with AWS Backup](CreateBackupAWS.md)
+ [Copying a backup of a DynamoDB table with AWS Backup](CrossRegionAccountCopyAWS.md)
+ [Restoring a backup of a DynamoDB table from AWS Backup](Restore.TutorialAWS.md)
+ [Deleting a backup of a DynamoDB table with AWS Backup](Delete.TutorialAWS.md)
+ [Usage notes](UsageNotesAWS.md)