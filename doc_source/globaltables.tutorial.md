# Creating a global table<a name="globaltables.tutorial"></a>

**Important**  
 This documentation is for version 2017\.11\.29 \(Legacy\) of global tables, which should be avoided for new global tables\. Customers should use [Version 2019\.11\.21 \(Current\)](globaltables.V2.md) when possible, as it provides greater flexibility, higher efficiency and consumes less write capacity than 2017\.11\.29 \(Legacy\)\.   
To determine which version you are using, see [Determining the global table version you are using](globaltables.DetermineVersion.md)\. To update existing global tables from version 2017\.11\.29 \(Legacy\) to version 2019\.11\.21 \(Current\), see [Updating global tables](V2globaltables_upgrade.md)\. 

This section describes how to create a global table using the Amazon DynamoDB console or the AWS Command Line Interface \(AWS CLI\)\. 

**Topics**
+ [Creating a global table \(console\)](#creategt_console)
+ [Creating a global table \(AWS CLI\)](#creategt_cli)

## Creating a global table \(console\)<a name="creategt_console"></a>

Follow these steps to create a global table using the console\. The following example creates a global table with replica tables in United States and Europe\.

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/home](https://console.aws.amazon.com/dynamodb/home)\. For this example, choose the us\-east\-2 \(US East Ohio\) Region\.

1. In the navigation pane on the left side of the console, choose **Tables**\.

1. Choose **Create Table**\. 

   For **Table name**, enter **Music**\. 

   For **Primary key** enter **Artist**\. Choose **Add sort key**, and enter **SongTitle**\. \(**Artist** and **SongTitle** should both be strings\.\)

   To create the table, choose **Create**\. This table serves as the first replica table in a new global table\. It is the prototype for other replica tables that you add later\.

1. Choose the **Global Tables** tab, and then choose **Create a Version 2017\.11\.29 \(Legacy\) replica**\.  
![\[Console screenshot showing the Create a Version 2017.11.29 (Legacy) replica button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/GlobalTables-old.png)

1. From the **Available replication Regions** dropdown, choose **US West \(Oregon\)**\.

   The console checks to ensure that a table with the same name doesn't exist in the selected Region\. If a table with the same name does exist, you must delete the existing table before you can create a new replica table in that Region\.

1. Choose **Create Replica**\. This starts the table creation process in US West \(Oregon\);\.

   The **Global Table** tab for the selected table \(and for any other replica tables\) shows that the table has been replicated in multiple Regions\.

1. Now add another Region so that your global table is replicated and synchronized across the United States and Europe\. To do this, repeat step 5, but this time, specify **Europe \(Frankfurt\)** instead of **US West \(Oregon\)**\.

1. You should still be using the AWS Management Console in the US East \(Ohio\) Region\. Select **Items** in the left navigation menu, select the **Music** table, then choose **Create Item**\.

   1. For **Artist**, enter **item\_1**\.

   1. For **SongTitle**, enter **Song Value 1**\.

   1. To write the item, choose **Create item**\.

1. After a short time, the item is replicated across all three Regions of your global table\. To verify this, in the console, on the Region selector in the upper\-right corner, choose **Europe \(Frankfurt\)**\. The `Music` table in Europe \(Frankfurt\) should contain the new item\.

1. Repeat step 9 and choose **US West \(Oregon\)** to verify replication in that region\.

## Creating a global table \(AWS CLI\)<a name="creategt_cli"></a>

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
 The global table name \(`Music`\) must match the name of each of the replica tables \(`Music`\)\. For more information, see [Best practices and requirements for managing global tables](globaltables_reqs_bestpractices.md)\. 

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