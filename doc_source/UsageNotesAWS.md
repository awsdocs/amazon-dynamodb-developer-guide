# Usage notes<a name="UsageNotesAWS"></a>

This section describes the technical differences between on\-demand backups managed by AWS Backup and DynamoDB\.

AWS Backup has some different workflows and behaviors than DynamoDB\. These include:

**Encryption** \- Backups created with the AWS Backup plan are stored in an encrypted vault with a key that is managed by the AWS Backup service\. The vault has access control policies for additional security\.

**Backup ARN** \- The backup files created by AWS Backup will now have an AWS Backup ARN, which could impact the user permission model\. Backup resource names \(ARNs\) will change from `arn:aws:dynamodb` to `arn:aws:backup`\.

**Deleting backups** \- Backups that are created with AWS Backup can only be deleted from the AWS Backup vault\. You will not be able to delete AWS Backup files from the DynamoDB console\.

**Backup process** \- Unlike DynamoDB backups, backups made with AWS Backup are not instantaneous\.

**Billing** \- Backups of DynamoDB tables with AWS Backup features are billed from AWS Backup\.

**IAM roles** \- If you're managing access through IAM roles, you will also need to configure a new IAM role with these new permissions: 

```
1. "dynamodb:StartAwsBackupJob", 
2. "dynamodb:RestoreTableFromAwsBackup"
```

`dynamodb:StartAwsBackupJob` is needed for a successful backup with AWS Backup features, and `dynamodb:RestoreTableFromAwsBackup` is needed to restore from a backupu made with AWS Backup features\.

To see these permissions in a complete IAM policy, see Example 8 in [Using IAM](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/backuprestore_IAM.html)\.