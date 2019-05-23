# Global Tables<a name="GlobalTables"></a>

*Amazon DynamoDB global tables* provide a fully managed solution for deploying a multi\-region, multi\-master database, without having to build and maintain your own replication solution\. When you create a global table, you specify the AWS regions where you want the table to be available\. DynamoDB performs all of the necessary tasks to create identical tables in these regions, and propagate ongoing data changes to all of them\.

To illustrate one use case for a global table, suppose that you have a large customer base spread across three geographic areas—the US east coast, the US west coast, and western Europe\. Customers would need to update their profile information while using your application\. To address these requirements, you could create three identical DynamoDB tables named *CustomerProfiles*, in three different AWS regions\. These three tables would be entirely separate from each other, and changes to the data in one table would not be reflected in the other tables\. Without a managed replication solution, you could write code to replicate data changes among these tables; however, this would be a time\-consuming and labor\-intensive effort\.

Instead of writing your own code, you could create a global table consisting of your three region\-specific *CustomerProfiles* tables\. DynamoDB would then automatically replicate data changes among those tables, so that changes to *CustomerProfiles* data in one region would be seamlessly propagated to the other regions\. In addition, if one of the AWS regions were to become temporarily unavailable, your customers could still access the same *CustomerProfiles* data in the other regions\.

DynamoDB global tables are ideal for massively scaled applications, with globally dispersed users\. In such an environment, users expect very fast application performance\. Global tables provide automatic multi\-master replication to AWS regions world\-wide, so you can deliver low\-latency data access to your users no matter where they are located\.

For information about the AWS Region availability and pricing, see [Amazon DynamoDB Pricing](https://aws.amazon.com/dynamodb/pricing)\. 

**Topics**
+ [Global Tables: How It Works](globaltables_HowItWorks.md)
+ [Requirements and Best Practices](globaltables_reqs_bestpractices.md)
+ [Creating a Global Table](globaltables.tutorial.md)
+ [Monitoring Global Tables](globaltables_monitoring.md)
+ [Using IAM with Global Tables](gt_IAM.md)