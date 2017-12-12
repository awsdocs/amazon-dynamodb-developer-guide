# Step 1: Create Example Tables<a name="SampleData.CreateTables"></a>

In this section, you will use the AWS Management Console to create tables in DynamoDB for two simple use cases\.

## Use Case 1: Product Catalog<a name="SampleData.CreateTables1"></a>

Suppose you want to store product information in DynamoDB\. Each product has its own distinct attributes, so you will need to store different information about each of these products\. 

You can create a *ProductCatalog* table, where each item is uniquely identified by a single, numeric attribute: *Id*\.


****  

| Table Name | Primary Key | 
| --- | --- | 
| ProductCatalog |  Partition key: `Id` \(Number\)  | 

## Use Case 2: Forum Application<a name="SampleData.CreateTables2"></a>

Suppose you want to build an application for message boards, or discussion forums\. The Amazon Web Services [Discussion Forums](https://forums.aws.amazon.com/) is one example of such an application: Customers can engage with the developer community, ask questions, or reply to other customers' posts\. Each AWS service has a dedicated forum\. Anyone can start a new discussion thread by posting a message in a forum\. Each thread might receive any number of replies\.

You can model this application by creating three tables: *Forum*, *Thread*, and *Reply*\.


****  

| Table Name | Primary Key | 
| --- | --- | 
| Forum |  Partition key: `Name` \(String\)  | 
| Thread |  Partition key: `ForumName` \(String\) Sort key: `Subject` \(String\)  | 
| Reply | Partition key: `Id` \(String\) Sort key: `ReplyDateTime` \(String\)  | 

The *Reply* table has a global secondary index named *PostedBy\-Message\-Index*\. This index will facilitate queries on two non\-key attributes of the *Reply* table\.


****  

| Index Name | Primary Key | 
| --- | --- | 
| PostedBy\-Message\-Index |  Partition key: `PostedBy` \(String\) Sort key: `Message` \(String\)  | 

## Create the ProductCatalog Table<a name="SampleData.CreateTablesSteps.ProductCatalog"></a>

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. Choose **Create Table**\.

1. In the **Create DynamoDB table** screen, do the following:

   1. In the **Table** name field, type `ProductCatalog`\.

   1. For the **Primary key**, in the **Partition key** field, type `Id`\. Set the data type to **Number**\.

1. When the settings are as you want them, choose **Create**\.

## Create the Forum Table<a name="SampleData.CreateTablesSteps.Forum"></a>

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. Choose **Create Table**\.

1. In the **Create DynamoDB table** screen, do the following:

   1. In the **Table** name field, type `Forum`\.

   1. For the **Primary key**, in the **Partition key** field, type `Name`\. Set the data type to **String**\.

1. When the settings are as you want them, choose **Create**\.

## Create the Thread Table<a name="SampleData.CreateTablesSteps.Thread"></a>

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. Choose **Create Table**\.

1. In the **Create DynamoDB table** screen, do the following:

   1. In the **Table name** field, type `Thread`\.

   1. For the **Primary key**, do the following:

      + In the **Partition key** field, type `ForumName`\. Set the data type to **String**\.

      + Choose **Add sort key**\.

      + In the **Sort key** field, type `Subject`\. Set the data type to **String**\.

1. When the settings are as you want them, choose **Create**\.

## Create the Reply Table<a name="SampleData.CreateTablesSteps.Reply"></a>

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. Choose **Create Table**\.

1. In the **Create DynamoDB table** screen, do the following:

   1. In the **Table name** field, type `Reply`\.

   1. For the **Primary key**, do the following:

      + In the **Partition key** field, type `Id`\. Set the data type to **String**\.

      + Choose **Add sort key**\.

      + In the **Sort key** field, type `ReplyDateTime`\. Set the data type to **String**\.

   1. In the **Table settings** section, deselect **Use default settings**\.

   1. In the **Secondary indexes** section, choose **Add index**\.

   1. In the **Add index** window, do the following:

      + For the **Primary key**, do the following:

        + In the **Partition key** field, type `PostedBy`\. Set the data type to **String**\.

        + Select **Add sort key**\.

        + In the **Sort key** field, type `Message`\. Set the data type to **String**\.

      + In the **Index name** field, type `PostedBy-Message-Index`\.

      + Set the **Projected attributes** to **All**\.

      + Choose **Add index**\.

1. When the settings are as you want them, choose **Create**\.