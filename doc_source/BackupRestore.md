# On\-Demand Backup and Restore for DynamoDB<a name="BackupRestore"></a>

Amazon DynamoDB provides on\-demand backup capability\. It allows you to create full backups of your tables for long\-term retention and archival for regulatory compliance needs\. You can back up and restore your DynamoDB table data anytime with a single click in the AWS Management Console or with a single API call\. Backup and restore actions execute with zero impact on table performance or availability\. 

 On\-demand backup and restore scales without degrading the performance or availability of your applications\. It uses a new and unique distributed technology that allows you to complete backups in seconds regardless of table size\. You can create backups that are consistent within seconds across thousands of partitions without worrying about schedules or long\-running backup processes\. All backups are cataloged, easily discoverable, and retained until explicitly deleted\. 

 In addition, on\-demand backup and restore operations don't affect performance or API latencies\. Backups are preserved regardless of table deletion\. For more information, see [Backup and Restore: How It Works](backuprestore_HowItWorks.md)\. 

 You can create table backups using the console, the AWS Command Line Interface \(AWS CLI\), or the DynamoDB API\. For more information, see [Backing Up a DynamoDB Table](Backup.Tutorial.md)\.

For information about restoring a table from a backup, see [Restoring a DynamoDB Table from a Backup](Restore.Tutorial.md)\. 

**Note**  
 Currently, on\-demand backup and restore functionality is supported in the following AWS Regions:   
 US East \(N\. Virginia\) 
 US East \(Ohio\) 
 US West \(Oregon\) 
 EU \(Ireland\) 

 Currently, the backup and restore functionality works in the same Region as the source table\. DynamoDB on\-demand backups are available at no additional cost beyond the normal pricing that's associated with backup storage size\. For more information, see [Amazon DynamoDB Pricing](https://aws.amazon.com/dynamodb/pricing)\. 