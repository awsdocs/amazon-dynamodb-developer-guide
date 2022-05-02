# What Is Amazon DynamoDB?<a name="Introduction"></a>

Welcome to the Amazon DynamoDB Developer Guide\.

Amazon DynamoDB is a fully managed NoSQL database service that provides fast and predictable performance with seamless scalability\. DynamoDB lets you offload the administrative burdens of operating and scaling a distributed database so that you don't have to worry about hardware provisioning, setup and configuration, replication, software patching, or cluster scaling\. DynamoDB also offers encryption at rest, which eliminates the operational burden and complexity involved in protecting sensitive data\. For more information, see [DynamoDB Encryption at Rest](EncryptionAtRest.md)\.

With DynamoDB, you can create database tables that can store and retrieve any amount of data and serve any level of request traffic\. You can scale up or scale down your tables' throughput capacity without downtime or performance degradation\. You can use the AWS Management Console to monitor resource utilization and performance metrics\.

DynamoDB provides on\-demand backup capability\. It allows you to create full backups of your tables for long\-term retention and archival for regulatory compliance needs\. For more information, see [Using On\-Demand Backup and Restore for DynamoDB](BackupRestore.md)\.

 You can create on\-demand backups and enable point\-in\-time recovery for your Amazon DynamoDB tables\. Point\-in\-time recovery helps protect your tables from accidental write or delete operations\. With point\-in\-time recovery, you can restore a table to any point in time during the last 35 days\. For more information, see [Point\-in\-Time Recovery: How It Works](PointInTimeRecovery_Howitworks.md)\.

 DynamoDB allows you to delete expired items from tables automatically to help you reduce storage usage and the cost of storing data that is no longer relevant\. For more information, see [Expiring Items By Using DynamoDB Time to Live \(TTL\)](TTL.md)\.

## High Availability and Durability<a name="ddb_highavailability"></a>

DynamoDB automatically spreads the data and traffic for your tables over a sufficient number of servers to handle your throughput and storage requirements, while maintaining consistent and fast performance\. All of your data is stored on solid\-state disks \(SSDs\) and is automatically replicated across multiple Availability Zones in an AWS Region, providing built\-in high availability and data durability\. You can use global tables to keep DynamoDB tables in sync across AWS Regions\. For more information, see [Global tables \- multi\-Region replication for DynamoDB](GlobalTables.md)\.

## Getting Started with DynamoDB<a name="ddb_getstarted"></a>

We recommend that you begin by reading the following sections:
+ **[Amazon DynamoDB: How It Works](HowItWorks.md)—**To learn essential DynamoDB concepts\.
+ **[Setting Up DynamoDB](SettingUp.md)—**To learn how to set up DynamoDB \(the downloadable version or the web service\)\.
+ **[Accessing DynamoDB](AccessingDynamoDB.md)—**To learn how to access DynamoDB using the console, AWS CLI, or API\.

To get started quickly with DynamoDB, see [Getting Started with DynamoDB and AWS SDKs](GettingStarted.md)\.

To learn more about application development, see the following:
+ [Programming with DynamoDB and the AWS SDKs](Programming.md)
+ [Working with Tables, Items, Queries, Scans, and Indexes](WorkingWithDynamo.md)

 To quickly find recommendations for maximizing performance and minimizing throughput costs, see [Best Practices for Designing and Architecting with DynamoDB](best-practices.md)\. To learn how to tag DynamoDB resources, see [Adding Tags and Labels to Resources](Tagging.md)\.

For best practices, how\-to guides, and tools, see [Amazon DynamoDB resources](https://aws.amazon.com/dynamodb/resources/)\. 

You can use AWS Database Migration Service \(AWS DMS\) to migrate data from a relational database or MongoDB to a DynamoDB table\. For more information, see the [AWS Database Migration Service User Guide](https://docs.aws.amazon.com/dms/latest/userguide/)\.

To learn how to use MongoDB as a migration source, see [Using MongoDB as a Source for AWS Database Migration Service](https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Source.MongoDB.html)\. To learn how to use DynamoDB as a migration target, see [Using an Amazon DynamoDB Database as a Target for AWS Database Migration Service](https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Target.DynamoDB.html)\.