# Global Tables: Multi\-Region Replication with DynamoDB<a name="GlobalTables"></a>


****  

|  | 
| --- |
| There are two versions of DynamoDB global tables available: [Version 2019\.11\.21 \(Current\)](globaltables.V2.md) and [Version 2017\.11\.29](globaltables.V1.md)\. To find out which version you are using, see [Determine Version](globaltables.DetermineVersion.md)\. | 

Amazon DynamoDB global tables provide a fully managed solution for deploying a multiregion, multi\-master database, without having to build and maintain your own replication solution\. With global tables you can specify the AWS Regions where you want the table to be available\. DynamoDB performs all of the necessary tasks to create identical tables in these Regions and propagate ongoing data changes to all of them\.

For example, suppose that you have a large customer base spread across three geographic areas—the US East Coast, the US West Coast, and Western Europe\. Customers can update their profile information using your application\. To satisfy this use case, you need to create three identical DynamoDB tables named `CustomerProfiles`, in three different AWS Regions where the customers are located\. These three tables would be entirely separate from each other\. Changes to the data in one table would not be reflected in the other tables\. Without a managed replication solution, you could write code to replicate data changes among these tables\. However, doing this would be a time\-consuming and labor\-intensive effort\.

Instead of writing your own code, you could create a global table consisting of your three Region\-specific `CustomerProfiles` tables\. DynamoDB would then automatically replicate data changes among those tables so that changes to `CustomerProfiles` data in one Region would seamlessly propagate to the other Regions\. In addition, if one of the AWS Regions were to become temporarily unavailable, your customers could still access the same `CustomerProfiles` data in the other Regions\.

DynamoDB global tables are ideal for massively scaled applications with globally dispersed users\. In such an environment, users expect very fast application performance\. Global tables provide automatic multi\-master replication to AWS Regions worldwide\. They enable you to deliver low\-latency data access to your users no matter where they are located\.

**Note**  
There are two versions of DynamoDB global tables available: [Version 2019\.11\.21 \(Current\)](globaltables.V2.md) and [Version 2017\.11\.29](globaltables.V1.md)\. We recommend using [Version 2019\.11\.21 \(Current\)](globaltables.V2.md) of global tables, which enables you to dynamically add new replica tables from a table populated with data\. [Version 2019\.11\.21 \(Current\)](globaltables.V2.md) is more efficient and consumes less write capacity than [Version 2017\.11\.29](globaltables.V1.md)\. 
Region support for global tables [Version 2017\.11\.29](globaltables.V1.md) is limited to US East \(N\. Virginia\), US East \(Ohio\), US West \(N\. California\), US West \(Oregon\), Europe \(Ireland\), Europe \(London\), Europe \(Frankfurt\), Asia Pacific \(Singapore\), Asia Pacific \(Sydney\), Asia Pacific \(Tokyo\), and Asia Pacific \(Seoul\)\.
If you are using [Version 2019\.11\.21 \(Current\)](globaltables.V2.md) of global tables and you also use the [Time to Live](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/TTL.html) feature, DynamoDB replicates TTL deletes to all replica tables\. The initial TTL delete does not consume write capacity in the region in which the TTL expiry occurs\. However, the replicated TTL delete to the replica table\(s\) consumes a replicated write capacity unit when using provisioned capacity, or replicated write when using on\-demand capacity mode, in each of the replica regions and applicable charges will apply\.
Transactional operations provide atomicity, consistency, isolation, and durability \(ACID\) guarantees only within the region where the write is made originally\. Transactions are not supported across regions in global tables\. For example, if you have a global table with replicas in the US East \(Ohio\) and US West \(Oregon\) regions and perform a TransactWriteItems operation in the US East \(N\. Virginia\) Region, you may observe partially completed transactions in US West \(Oregon\) Region as changes are replicated\. Changes will only be replicated to other regions once they have been committed in the source region\.

For information about the AWS Region availability and pricing, see [Amazon DynamoDB Pricing](https://aws.amazon.com/dynamodb/pricing)\. 

**Topics**
+ [Determining the Version of Global Tables You Are Using](globaltables.DetermineVersion.md)
+ [Version 2019\.11\.21 \(Current\)](globaltables.V2.md)
+ [Version 2017\.11\.29](globaltables.V1.md)