# Step 1: Create example tables<a name="SampleData.CreateTables"></a>

In this section, you use the AWS Management Console to create tables in Amazon DynamoDB for two simple use cases\.

## Use case 1: Product catalog<a name="SampleData.CreateTables1"></a>

Suppose that you want to store product information in DynamoDB\. Each product has its own distinct attributes, so you need to store different information about each of these products\. 

You can create a `ProductCatalog` table, where each item is uniquely identified by a single, numeric attribute: `Id`\.


****  

| Table name | Primary key | 
| --- | --- | 
| ProductCatalog |  Partition key: `Id` \(Number\)  | 

## Use case 2: Forum application<a name="SampleData.CreateTables2"></a>

Suppose that you want to build an application for message boards or discussion forums\. AWS [Discussion forums](https://forums.aws.amazon.com/) represent one example of such an application\. Customers can engage with the developer community, ask questions, or reply to other customers' posts\. Each AWS service has a dedicated forum\. Anyone can start a new discussion thread by posting a message in a forum\. Each thread might receive any number of replies\.

You can model this application by creating three tables: `Forum`, `Thread`, and `Reply`\.


****  

| Table name | Primary key | 
| --- | --- | 
| Forum |  Partition key: `Name` \(String\)  | 
| Thread |  Partition key: `ForumName` \(String\) Sort key: `Subject` \(String\)  | 
| Reply | Partition key: `Id` \(String\) Sort key: `ReplyDateTime` \(String\)  | 

The `Reply` table has a global secondary index named `PostedBy-Message-Index`\. This index facilitates queries on two non\-key attributes of the `Reply` table\.


****  

| Index name | Primary key | 
| --- | --- | 
| PostedBy\-Message\-Index |  Partition key: `PostedBy` \(String\) Sort key: `Message` \(String\)  | 

## Create the ProductCatalog table<a name="SampleData.CreateTablesSteps.ProductCatalog"></a>

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. Choose **Create Table**\.

1. In the **Create DynamoDB table** screen, do the following:

   1. On the **Table** name box, enter `ProductCatalog`\.

   1. For the **Primary key**, in the **Partition key** box, enter **Id**\. Set the data type to **Number**\.

1. When the settings are as you want them, choose **Create**\.

## Create the forum table<a name="SampleData.CreateTablesSteps.Forum"></a>

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. Choose **Create Table**\.

1. In the **Create DynamoDB table** screen, do the following:

   1. In the **Table** name box, enter **Forum**\.

   1. For the **Primary key**, in the **Partition key** box, enter **Name**\. Set the data type to **String**\.

1. When the settings are as you want them, choose **Create**\.

## Create the thread table<a name="SampleData.CreateTablesSteps.Thread"></a>

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. Choose **Create Table**\.

1. In the **Create DynamoDB table** screen, do the following:

   1. In the **Table name** box, enter **Thread**\.

   1. For the **Primary key**, do the following:
      + In the **Partition key** box, enter **ForumName**\. Set the data type to **String**\.
      + Choose **Add sort key**\.
      + In the **Sort key** box, enter **Subject**\. Set the data type to **String**\.

1. When the settings are as you want them, choose **Create**\.

## Create the reply table<a name="SampleData.CreateTablesSteps.Reply"></a>

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. Choose **Create Table**\.

1. In the **Create DynamoDB table** screen, do the following:

   1. In the **Table name** box, enter **Reply**\.

   1. For the **Primary key**, do the following:
      + In the **Partition key** box, enter **Id**\. Set the data type to **String**\.
      + Choose **Add sort key**\.
      + In the **Sort key** box, enter **ReplyDateTime**\. Set the data type to **String**\.

   1. In the **Table settings** section, clear **Use default settings**\.

   1. In the **Secondary indexes** section, choose **Add index**\.

   1. In the **Add index** window, do the following:
      + For the **Primary key**, do the following:
        + In the **Partition key** box, enter **PostedBy**\. Set the data type to **String**\.
        + Choose **Add sort key**\.
        + In the **Sort key** box, enter **Message**\. Set the data type to **String**\.
      + In the **Index name** box, enter **PostedBy\-Message\-Index**\.
      + Set the **Projected attributes** to **All**\.
      + Choose **Add index**\.

1. When the settings are as you want them, choose **Create**\.