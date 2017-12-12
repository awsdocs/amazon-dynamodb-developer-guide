# What Is Amazon DynamoDB?<a name="Introduction"></a>

Welcome to the Amazon DynamoDB Developer Guide\.

Amazon DynamoDB is a fully managed NoSQL database service that provides fast and predictable performance with seamless scalability\. DynamoDB lets you offload the administrative burdens of operating and scaling a distributed database, so that you don't have to worry about hardware provisioning, setup and configuration, replication, software patching, or cluster scaling\.

With DynamoDB, you can create database tables that can store and retrieve any amount of data, and serve any level of request traffic\. You can scale up or scale down your tables' throughput capacity without downtime or performance degradation, and use the AWS Management Console to monitor resource utilization and performance metrics\.

 DynamoDB allows you to delete expired items from tables automatically to help you reduce storage usage and the cost of storing data that is no longer relevant\. For more information, see [Time To Live](TTL.md)\. 

DynamoDB automatically spreads the data and traffic for your tables over a sufficient number of servers to handle your throughput and storage requirements, while maintaining consistent and fast performance\. All of your data is stored on solid state disks \(SSDs\) and automatically replicated across multiple Availability Zones in an AWS region, providing built\-in high availability and data durability\. 

We recommend that you begin by reading the following sections:

+ **[Amazon DynamoDB: How It Works](HowItWorks.md)—**To learn essential DynamoDB concepts\.

+ **[Setting Up DynamoDB](SettingUp.md)—**To learn how to setup DynamoDB \(Downloadable Version or Web Service\)\.

+ **[Accessing DynamoDB](AccessingDynamoDB.md)—**To learn how to access DynamoDB using the console, CLI, or API\. 

 To get started quickly with DynamoDB, see [Getting Started with DynamoDB](GettingStarted.md)\. 

To learn more about application development see the following:

+ [Programming with DynamoDB and the AWS SDKs](Programming.md)

+ [Working with DynamoDB](WorkingWithDynamo.md)

 To quickly find recommendations for maximizing performance and minimizing throughput costs see [Best Practices for DynamoDB](BestPractices.md)\. To learn how to tag DynamoDB resources see [Tagging for DynamoDB](Tagging.md)\. 

For best practices, how\-to guides and tools, be sure to check the DynamoDB Developer Resources page: [http://aws\.amazon\.com/dynamodb/developer\-resources/](https://aws.amazon.com/dynamodb/developer-resources/)\. 

 You can use AWS Database Migration Service to migrate data from a Relational Database or MongoDB to an Amazon DynamoDB table\. For more information, see [AWS Database Migration Service User Guide](http://docs.aws.amazon.com/dms/latest/userguide/)\. To learn how to use MongoDB as a migration source, see [Using MongoDB as a Source for AWS Database Migration Service](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Source.MongoDB.html)\. To learn how to use DynamoDB as a migration target, see [Using an Amazon DynamoDB Database as a Target for AWS Database Migration Service](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Target.DynamoDB.html)\. 