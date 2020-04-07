# Creating a Global Table<a name="globaltables.tutorial"></a>


****  

|  | 
| --- |
| There are two versions of DynamoDB global tables available: [Version 2019\.11\.21 \(Current\)](globaltables.V2.md) and [Version 2017\.11\.29](globaltables.V1.md)\. To find out which version you are using, see [Determine Version](globaltables.DetermineVersion.md)\. | 

This section describes how to create a global table using the Amazon DynamoDB console or the AWS Command Line Interface \(AWS CLI\)\. 

**Topics**
+ [Creating a Global Table \(Console\)](#creategt_console)
+ [Creating a Global Table \(AWS CLI\)](#creategt_cli)

## Creating a Global Table \(Console\)<a name="creategt_console"></a>

Follow these steps to create a global table using the console\. The following example creates a global table with replica tables in United States and Europe\.

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/home](https://console.aws.amazon.com/dynamodb/home)\. For this example, choose the us\-east\-2 \(US East Ohio\) Region\.

1. In the navigation pane on the left side of the console, choose **Tables**\.

1. Choose **Create Table**\. 

   For **Table name**, enter **Music**\. 

   For **Primary key** enter **Artist**\. Choose **Add sort key**, and enter **SongTitle**\. \(**Artist** and **SongTitle** should both be strings\.\)

   To create the table, choose **Create**\. This table serves as the first replica table in a new global table\. It is the prototype for other replica tables that you add later\.

1. Choose the **Global Tables** tab, and then choose **Enable streams**\. Keep the **View type** at its default value \(New and old images\)\. 

1. Choose **Add region**, and choose **Global table 2017\.11\.29 mode**\. You can now choose another Region where you want to deploy another replica table\. In this case, choose **US West \(Oregon\)**, and then choose **Continue**\. This starts the table creation process in US West \(Oregon\)\.

   The console checks to ensure that there is no table with the same name in the selected Region\. \(If a table with the same name does exist, you must delete the existing table before you can create a new replica table in that Region\.\)

   The **Global Table** tab for the selected table \(and for any other replica tables\) will show that the table is replicated in multiple Regions\. 

1. Now add another Region so that your global table is replicated and synchronized across the United States and Europe\. To do this, repeat step 5, but this time, specify **EU \(Frankfurt\)** instead of **US West \(Oregon\)**\.

1. You should still be using the AWS Management Console in the us\-east\-2 \(US East Ohio\) Region\. For the **Music** table, choose the **Items** tab, and then choose **Create Item**\. For **Artist**, enter **item\_1**\. For **SongTitle**, enter **Song Value 1**\. To write the item, choose **Save**\.

   After a short time, the item is replicated across all three Regions of your global table\. To verify this, in the console, on the Region selector in the upper\-right corner, choose **Europe \(Frankfurt\)**\. The `Music` table in Europe \(Frankfurt\) should contain the new item\.

   Repeat this for US West \(Oregon\)\.

## Creating a Global Table \(AWS CLI\)<a name="creategt_cli"></a>

Follow these steps to create a global table `Music` using the AWS CLI\. The following example creates a global table, with replica tables in the United States and in Europe\.

1. Create a new table \(`Music`\) in US East \(Ohio\), with DynamoDB Streams enabled \(`NEW_AND_OLD_IMAGES`\)\.

   ```
   aws dynamodb create-table \
       --table-name Music \
       --attribute-definitions \
           AttributeName=Artist,AttributeType=S \
           AttributeName=SongTitle,AttributeType=S \
       --key-schema \
           AttributeName=Artist,KeyType=HASH \
           AttributeName=SongTitle,KeyType=RANGE \
       --provisioned-throughput \
           ReadCapacityUnits=10,WriteCapacityUnits=5 \
       --stream-specification StreamEnabled=true,StreamViewType=NEW_AND_OLD_IMAGES \
       --region us-east-2
   ```

1. Create an identical `Music` table in US East \(N\. Virginia\)\.

   ```
   aws dynamodb create-table \
       --table-name Music \
       --attribute-definitions \
           AttributeName=Artist,AttributeType=S \
           AttributeName=SongTitle,AttributeType=S \
       --key-schema \
           AttributeName=Artist,KeyType=HASH \
           AttributeName=SongTitle,KeyType=RANGE \
       --provisioned-throughput \
           ReadCapacityUnits=10,WriteCapacityUnits=5 \
       --stream-specification StreamEnabled=true,StreamViewType=NEW_AND_OLD_IMAGES \
       --region us-east-1
   ```

1. Create a global table \(`Music`\) consisting of replica tables in the `us-east-2` and `us-east-1` Regions\.

   ```
   aws dynamodb create-global-table \
       --global-table-name Music \
       --replication-group RegionName=us-east-2 RegionName=us-east-1 \
       --region us-east-2
   ```
**Note**  
 The global table name \(`Music`\) must match the name of each of the replica tables \(`Music`\)\. For more information, see [Best Practices and Requirements for Managing Global Tables](globaltables_reqs_bestpractices.md)\. 

1. Create another table in Europe \(Ireland\), with the same settings as those you created in step 1 and step 2\.

   ```
   aws dynamodb create-table \
       --table-name Music \
       --attribute-definitions \
           AttributeName=Artist,AttributeType=S \
           AttributeName=SongTitle,AttributeType=S \
       --key-schema \
           AttributeName=Artist,KeyType=HASH \
           AttributeName=SongTitle,KeyType=RANGE \
       --provisioned-throughput \
           ReadCapacityUnits=10,WriteCapacityUnits=5 \
       --stream-specification StreamEnabled=true,StreamViewType=NEW_AND_OLD_IMAGES \
       --region eu-west-1
   ```

   After doing this step, add the new table to the `Music` global table\.

   ```
   aws dynamodb update-global-table \
       --global-table-name Music \
       --replica-updates 'Create={RegionName=eu-west-1}' \
       --region us-east-2
   ```

1. To verify that replication is working, add a new item to the `Music` table in US East \(Ohio\)\.

   ```
   aws dynamodb put-item \
       --table-name Music \
       --item '{"Artist": {"S":"item_1"},"SongTitle": {"S":"Song Value 1"}}' \
       --region us-east-2
   ```

1. Wait for a few seconds, and then check to see whether the item has been successfully replicated to US East \(N\. Virginia\) and Europe \(Ireland\)\.

   ```
   aws dynamodb get-item \
       --table-name Music \
       --key '{"Artist": {"S":"item_1"},"SongTitle": {"S":"Song Value 1"}}' \
       --region us-east-1
   ```

   ```
   aws dynamodb get-item \
       --table-name Music \
       --key '{"Artist": {"S":"item_1"},"SongTitle": {"S":"Song Value 1"}}' \
       --region eu-west-1
   ```