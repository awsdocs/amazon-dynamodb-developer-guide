# What is Amazon DynamoDB?<a name="Introduction"></a>

Welcome to the Amazon DynamoDB Developer Guide\.

Amazon DynamoDB is a fully managed NoSQL database service that provides fast and predictable performance with seamless scalability\. DynamoDB lets you offload the administrative burdens of operating and scaling a distributed database so that you don't have to worry about hardware provisioning, setup and configuration, replication, software patching, or cluster scaling\. DynamoDB also offers encryption at rest, which eliminates the operational burden and complexity involved in protecting sensitive data\. For more information, see [DynamoDB encryption at rest](EncryptionAtRest.md)\.

With DynamoDB, you can create database tables that can store and retrieve any amount of data and serve any level of request traffic\. You can scale up or scale down your tables' throughput capacity without downtime or performance degradation\. You can use the AWS Management Console to monitor resource utilization and performance metrics\.

DynamoDB provides on\-demand backup capability\. It allows you to create full backups of your tables for long\-term retention and archival for regulatory compliance needs\. For more information, see [Using On\-Demand backup and restore for DynamoDB](BackupRestore.md)\.

 You can create on\-demand backups and enable point\-in\-time recovery for your Amazon DynamoDB tables\. Point\-in\-time recovery helps protect your tables from accidental write or delete operations\. With point\-in\-time recovery, you can restore a table to any point in time during the last 35 days\. For more information, see [Point\-in\-time recovery: How it works](PointInTimeRecovery_Howitworks.md)\.

 DynamoDB allows you to delete expired items from tables automatically to help you reduce storage usage and the cost of storing data that is no longer relevant\. For more information, see [Expiring items by using DynamoDB Time to Live \(TTL\)](TTL.md)\.

## High availability and durability<a name="ddb_highavailability"></a>

DynamoDB automatically spreads the data and traffic for your tables over a sufficient number of servers to handle your throughput and storage requirements, while maintaining consistent and fast performance\. All of your data is stored on solid\-state disks \(SSDs\) and is automatically replicated across multiple Availability Zones in an AWS Region, providing built\-in high availability and data durability\. You can use global tables to keep DynamoDB tables in sync across AWS Regions\. For more information, see [Global tables \- multi\-Region replication for DynamoDB](GlobalTables.md)\.

## Getting started with DynamoDB<a name="ddb_getstarted"></a>

We recommend that you begin by reading the following sections:
+ **[Amazon DynamoDB: How it works](HowItWorks.md)—**To learn essential DynamoDB concepts\.
+ **[Setting up DynamoDB](SettingUp.md)—**To learn how to set up DynamoDB \(the downloadable version or the web service\)\.
+ **[Accessing DynamoDB](AccessingDynamoDB.md)—**To learn how to access DynamoDB using the console, AWS CLI, or API\.

From there, you have two options to quickly get started quickly with DynamoDB:
+ [Getting started with DynamoDB](GettingStartedDynamoDB.md)
+ [Getting started with DynamoDB and the AWS SDKs](GettingStarted.md)

To learn more about application development, see the following:
+ [Programming with DynamoDB and the AWS SDKs](Programming.md)
+ [Working with tables, items, queries, scans, and indexes](WorkingWithDynamo.md)

 To quickly find recommendations for maximizing performance and minimizing throughput costs, see the following: [Best practices for designing and architecting with DynamoDB](best-practices.md)\. To learn how to tag DynamoDB resources, see [Adding tags and labels to resources](Tagging.md)\.

For best practices, how\-to guides, and tools, see [Amazon DynamoDB resources](https://aws.amazon.com/dynamodb/resources/)\. 

You can use AWS Database Migration Service \(AWS DMS\) to migrate data from a relational database or MongoDB to a DynamoDB table\. For more information, see the [AWS Database Migration Service User Guide](https://docs.aws.amazon.com/dms/latest/userguide/)\.

To learn how to use MongoDB as a migration source, see [Using MongoDB as a source for AWS Database Migration Service](https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Source.MongoDB.html)\. To learn how to use DynamoDB as a migration target, see [Using an Amazon DynamoDB database as a target for AWS Database Migration Service](https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Target.DynamoDB.html)\.

## DynamoDB tutorials<a name="ddb_tutorials"></a>

The following tutorials present complete end\-to\-end procedures to familiarize yourself with DynamoDB\. These tutorials can be completed with the free tier of AWS and will give you practical experience using DynamoDB\.
+ [Build an Application Using a NoSQL Key\-Value Data Store](http://aws.amazon.com/getting-started/guides/build-an-application-using-a-no-sql-key-value-data-store/?ref=docs_gateway/dynamodb/Introduction.html) 
+ [Create and Query a NoSQL Table with Amazon DynamoDB](http://aws.amazon.com/getting-started/hands-on/create-nosql-table/?ref=docs_gateway/dynamodb/Introduction.html) 