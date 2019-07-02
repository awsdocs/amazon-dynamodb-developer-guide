# Resilience and Disaster Recovery in Amazon DynamoDB<a name="disaster-recovery-resiliency"></a>

The AWS global infrastructure is built around AWS Regions and Availability Zones\. AWS Regions provide multiple physically separated and isolated Availability Zones, which are connected with low\-latency, high\-throughput, and highly redundant networking\. With Availability Zones, you can design and operate applications and databases that automatically fail over between Availability Zones without interruption\. Availability Zones are more highly available, fault tolerant, and scalable than traditional single or multiple data center infrastructures\. 

If you need to replicate your data or applications over greater geographic distances, use AWS Local Regions\. An AWS Local Region is a single data center designed to complement an existing AWS Region\. Like all AWS Regions, AWS Local Regions are completely isolated from other AWS Regions\.

For more information about AWS Regions and Availability Zones, see [AWS Global Infrastructure](https://aws.amazon.com/about-aws/global-infrastructure/)\.

In addition to the AWS global infrastructure, Amazon DynamoDB offers several features to help support your data resiliency and backup needs\.

**On\-demand backup and restore**  
DynamoDB provides on\-demand backup capability\. It allows you to create full backups of your tables for long\-term retention and archival\. For more information, see [On\-Demand Backup and Restore for DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/BackupRestore.html)\.

**Point\-in\-time recovery**  
Point\-in\-time recovery helps protect your DynamoDB tables from accidental write or delete operations\. With point in time recovery, you don't have to worry about creating, maintaining, or scheduling on\-demand backups\. For more information, see [Point\-in\-Time Recovery for DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/PointInTimeRecovery.html)\.