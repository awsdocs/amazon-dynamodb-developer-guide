# Using On\-Demand backup and restore for DynamoDB<a name="BackupRestore"></a>

You can use the DynamoDB on\-demand backup capability to create full backups of your tables for long\-term retention, and archiving for regulatory compliance needs\. You can back up and restore your table data anytime with a single click on the AWS Management Console or with a single API call\. Backup and restore actions run with no impact on table performance or availability\.

The following video will give you an introductory look at the backup and restore concept\.

[![AWS Videos](http://img.youtube.com/vi/https://www.youtube.com/embed/4INEu_hw30Q/0.jpg)](http://www.youtube.com/watch?v=https://www.youtube.com/embed/4INEu_hw30Q)

There are two options available for creating and managing DynamoDB on\-demand backups: 
+ AWS Backup service
+ DynamoDB

With AWS Backup, you can configure backup policies and monitor activity for your AWS resources and on\-premises workloads in one place\. Using DynamoDB with AWS Backup, you can copy your on\-demand backups across AWS accounts and Regions, add cost allocation tags to on\-demand backups, and transition on\-demand backups to cold storage for lower costs\. To use these advanced features, you must [opt in](https://docs.aws.amazon.com/aws-backup/latest/devguide/service-opt-in.html) to AWS Backup\. Opt\-in choices apply to the specific account and AWS Region, so you might have to opt in to multiple Regions using the same account\. For more information, see the [AWS Backup Developer Guide](https://docs.aws.amazon.com/aws-backup/latest/devguide/service-opt-in.html)\.

The on\-demand backup and restore process scales without degrading the performance or availability of your applications\. It uses a new and unique distributed technology that lets you complete backups in seconds regardless of table size\. You can create backups that are consistent within seconds across thousands of partitions without worrying about schedules or long\-running backup processes\. All on\-demand backups are cataloged, discoverable, and retained until they are explicitly deleted\.

In addition, on\-demand backup and restore operations don't affect performance or API latencies\. Backups are preserved regardless of table deletion\. For more information, see [Using DynamoDB backup and restore](backuprestore_HowItWorks.md)\.

DynamoDB on\-demand backups are available at no additional cost beyond the normal pricing that's associated with backup storage size\. DynamoDB on\-demand backups cannot be copied to a different account or Region\. To create backup copies across AWS accounts and Regions and for other advanced features, you should use AWS Backup\. If you use AWS Backup features you will be billed for them by AWS Backup\. For more information about AWS Region availability and pricing, see [Amazon DynamoDB pricing](https://aws.amazon.com/dynamodb/pricing)\.

**Topics**
+ [Using AWS Backup with DynamoDB](backuprestore_HowItWorksAWS.md)
+ [Using DynamoDB backup and restore](backuprestore_HowItWorks.md)