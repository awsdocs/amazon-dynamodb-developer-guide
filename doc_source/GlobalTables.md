# Global tables \- multi\-Region replication for DynamoDB<a name="GlobalTables"></a>


****  

|  | 
| --- |
| There are two versions of DynamoDB global tables available: [Version 2019\.11\.21 \(Current\)](globaltables.V2.md) and [Version 2017\.11\.29](globaltables.V1.md)\. To find out which version you are using, see [Determining the version](globaltables.DetermineVersion.md)\. | 

Amazon DynamoDB *global tables* provide a fully managed solution for deploying a multi\-Region, multi\-active database, without having to build and maintain your own replication solution\. You can specify the AWS Regions where you want the tables to be available and DynamoDB will propagate ongoing data changes to all of them\.

DynamoDB global tables are ideal for massively scaled applications with globally dispersed users\. In such an environment, users expect very fast application performance\. Global tables provide automatic multi\-active replication to AWS Regions worldwide\. They enable you to deliver low\-latency data access to your users no matter where they are located\.

**Topics**
+ [Replicate data seamlessly across Regions with global tables](#GlobalTablesOverview)
+ [Provide security and access for your global tables with AWS KMS](#GlobalTables.Security.Access)
+ [Determining the global table version you are using](globaltables.DetermineVersion.md)
+ [Version 2019\.11\.21 \(Current\)](globaltables.V2.md)
+ [Version 2017\.11\.29](globaltables.V1.md)

## Replicate data seamlessly across Regions with global tables<a name="GlobalTablesOverview"></a>

Suppose you have a large customer base spread across three geographic areas—the US East Coast, the US West Coast, and Western Europe\. These customers can update their profile information using your application\. To satisfy this use case, you need to create three identical DynamoDB tables named `CustomerProfiles`, in three different AWS Regions where the customers are located\. These three tables would be entirely separate from each other—changes to the data in one table would not be reflected in the others\. Without a managed replication solution, you would have to write code to replicate the data changes\. However, doing this would be a time\-consuming and labor\-intensive effort\.

Instead of writing your own code, you could create a global table consisting of your three Region\-specific `CustomerProfiles` tables\. DynamoDB would then automatically replicate data changes among those tables so that changes to `CustomerProfiles` data in one Region would seamlessly propagate to the other Regions\. In addition, if one of the AWS Regions were to become temporarily unavailable, your customers could still access the same `CustomerProfiles` data in the other Regions\.

If the replicas in different regions have different access patterns, each replica can use a different table class to optimize cost and performance\. For example, an infrequently accessed replica can use the DynamoDB Standard\-Infrequent Access table class, while other replicas use the DynamoDB Standard table class for frequently\-accessed data, where throughput is the dominant cost\.

**Note**  
Region support for global tables [Version 2017\.11\.29](globaltables.V1.md) is limited to US East \(N\. Virginia\), US East \(Ohio\), US West \(N\. California\), US West \(Oregon\), Europe \(Ireland\), Europe \(London\), Europe \(Frankfurt\), Asia Pacific \(Singapore\), Asia Pacific \(Sydney\), Asia Pacific \(Tokyo\), and Asia Pacific \(Seoul\)\.
Transactional operations provide atomicity, consistency, isolation, and durability \(ACID\) guarantees only within the region where the write is made originally\. Transactions are not supported across regions in global tables\. For example, if you have a global table with replicas in the US East \(Ohio\) and US West \(Oregon\) regions and perform a TransactWriteItems operation in the US East \(N\. Virginia\) Region, you may observe partially completed transactions in US West \(Oregon\) Region as changes are replicated\. Changes will only be replicated to other regions once they have been committed in the source region\.
If you [disable an AWS Region](https://docs.aws.amazon.com/general/latest/gr/rande-manage.html), DynamoDB will remove this replica from the replication group, 20 hours after detecting the AWS Region as inaccessible\. The replica will not be deleted and replication will stop from and to this region\.

For information about the AWS Region availability and pricing, see [Amazon DynamoDB Pricing](https://aws.amazon.com/dynamodb/pricing)\. 

## Provide security and access for your global tables with AWS KMS<a name="GlobalTables.Security.Access"></a>
+ You can perform AWS KMS operations on your global tables by using the `AWSServiceRoleForDynamoDBReplication` service\-linked role against the [customer managed key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#aws-managed-cmk) or the [AWS managed key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#customer-cmk) used to encrypt the replica\.
+ If the customer managed key used to encrypt a replica is inaccessible, DynamoDB will remove this replica from the replication group\. The replica will not be deleted and replication will stop from and to this region, 20 hours after detecting the KMS key as inaccessible\.
+ If you want to disable your [customer managed key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#aws-managed-cmk) that is used to encrypt a replica table, you must do so only if the key is no longer used to encrypt a replica table\. After issuing a command to delete a replica table, you must wait for the delete operation to complete and for the global table to become `Active` before disabling the key\. Not doing so could result in partial data replication from and to the replica table\.
+ If you want to modify or delete your IAM role policy for the replica table, you must do so when the replica table is in the `Active` state\. If you don’t do this, creating, updating, or deleting the replica table could fail\.

**Note**  
Customer Managed Key \(CMK\) is not supported in [Version 2017\.11\.29](globaltables.V1.md)\. If you want to use a CMK in a DynamoDB Global Table, you need to upgrade the table to [Version 2019\.11\.21 \(Current\)](globaltables.V2.md) and then enable it\.