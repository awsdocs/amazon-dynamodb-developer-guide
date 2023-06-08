# Tutorial: Creating a global table<a name="V2globaltables.tutorial"></a>


****  

|  | 
| --- |
| There are two versions of DynamoDB global tables available: [Version 2019\.11\.21 \(Current\)](globaltables.V2.md) and [Version 2017\.11\.29 \(Legacy\)](globaltables.V1.md)\. Customers should use version 2019\.11\.21 \(Current\) when possible, as it provides greater flexibility, higher efficiency and consumes less write capacity than 2017\.11\.29 \(Legacy\)\. To determine which version you are using, see [Determining the global table version you are using](globaltables.DetermineVersion.md)\. | 

This section describes how to create a global table using the Amazon DynamoDB console or the AWS Command Line Interface \(AWS CLI\)\. 

**Topics**
+ [Creating a global table \(console\)](#V2creategt_console)
+ [Creating a global table \(AWS CLI\)](#V2creategt_cli)
+ [Creating a global table \(Java\)](#V2creategt_java)

## Creating a global table \(console\)<a name="V2creategt_console"></a>

Follow these steps to create a global table using the console\. The following example creates a global table with replica tables in United States and Europe\.

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/home](https://console.aws.amazon.com/dynamodb/home)\. For this example, choose the US East \(Ohio\) Region\.

1. In the navigation pane on the left side of the console, choose **Tables**\.

1. Choose **Create Table**\.

   1. For **Table name**, enter **Music**\.

   1. For **Partition key** enter **Artist**\. For **Sort key** enter **SongTitle**\. \(**Artist** and **SongTitle** should both be strings\.\)

   To create the table, choose **Create table**\. This table serves as the first replica table in a new global table\. It is the prototype for other replica tables that you add later\.

1. Choose the **Global Tables** tab, and then choose **Create replica**\.

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

## Creating a global table \(AWS CLI\)<a name="V2creategt_cli"></a>

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
       --billing-mode PAY_PER_REQUEST \
       --stream-specification StreamEnabled=true,StreamViewType=NEW_AND_OLD_IMAGES \
       --region us-east-2
   ```

1. Create an identical `Music` table in US East \(N\. Virginia\)\.

   ```
   aws dynamodb update-table --table-name Music --cli-input-json  \
   '{
     "ReplicaUpdates":
     [
       {
         "Create": {
           "RegionName": "us-east-1"
         }
       }
     ]
   }' \
   --region=us-east-2
   ```

1. Repeat step 2 to create a table in Europe \(Ireland\) \(eu\-west\-1\)\.

1. You can view the list of replicas created using `describe-table`\.

   ```
   aws dynamodb describe-table --table-name Music --region us-east-2 
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

1. Delete the replica table in Europe \(Ireland\) Region\.

   ```
   aws dynamodb update-table --table-name Music --cli-input-json \
   '{
     "ReplicaUpdates":
     [
       {
         "Delete": {
           "RegionName": "eu-west-1"
         }
       }
     ]
   }'
   ```

## Creating a global table \(Java\)<a name="V2creategt_java"></a>

The following java code sample, create a `Music` table in Europe \(Ireland\) region then creates a replica in Asia Pacific \(Seoul\) region\.

```
package com.amazonaws.codesamples.gtv2
import java.util.logging.Logger;
import com.amazonaws.auth.profile.ProfileCredentialsProvider;
import com.amazonaws.regions.Regions;
import com.amazonaws.services.dynamodbv2.AmazonDynamoDB;
import com.amazonaws.services.dynamodbv2.AmazonDynamoDBClientBuilder;
import com.amazonaws.services.dynamodbv2.model.AmazonDynamoDBException;
import com.amazonaws.services.dynamodbv2.model.AttributeDefinition;
import com.amazonaws.services.dynamodbv2.model.BillingMode;
import com.amazonaws.services.dynamodbv2.model.CreateReplicationGroupMemberAction;
import com.amazonaws.services.dynamodbv2.model.CreateTableRequest;
import com.amazonaws.services.dynamodbv2.model.DescribeTableRequest;
import com.amazonaws.services.dynamodbv2.model.GlobalSecondaryIndex;
import com.amazonaws.services.dynamodbv2.model.KeySchemaElement;
import com.amazonaws.services.dynamodbv2.model.KeyType;
import com.amazonaws.services.dynamodbv2.model.Projection;
import com.amazonaws.services.dynamodbv2.model.ProjectionType;
import com.amazonaws.services.dynamodbv2.model.ProvisionedThroughput;
import com.amazonaws.services.dynamodbv2.model.ProvisionedThroughputOverride;
import com.amazonaws.services.dynamodbv2.model.ReplicaGlobalSecondaryIndex;
import com.amazonaws.services.dynamodbv2.model.ReplicationGroupUpdate;
import com.amazonaws.services.dynamodbv2.model.ScalarAttributeType;
import com.amazonaws.services.dynamodbv2.model.StreamSpecification;
import com.amazonaws.services.dynamodbv2.model.StreamViewType;
import com.amazonaws.services.dynamodbv2.model.UpdateTableRequest;
import com.amazonaws.waiters.WaiterParameters;


public class App
{
    private final static Logger LOGGER = Logger.getLogger(Logger.GLOBAL_LOGGER_NAME);

    public static void main( String[] args )
    {

        String tableName = "Music";
        String indexName = "index1";

        Regions calledRegion = Regions.EU_WEST_1;
        Regions destRegion = Regions.AP_NORTHEAST_2;

        AmazonDynamoDB ddbClient = AmazonDynamoDBClientBuilder.standard()
                .withCredentials(new ProfileCredentialsProvider("default"))
                .withRegion(calledRegion)
                .build();


        LOGGER.info("Creating a regional table - TableName: " + tableName +", IndexName: " + indexName + " .....");
        ddbClient.createTable(new CreateTableRequest()
                .withTableName(tableName)
                .withAttributeDefinitions(
                        new AttributeDefinition()
                                .withAttributeName("Artist").withAttributeType(ScalarAttributeType.S),
                        new AttributeDefinition()
                                .withAttributeName("SongTitle").withAttributeType(ScalarAttributeType.S))
                .withKeySchema(
                        new KeySchemaElement().withAttributeName("Artist").withKeyType(KeyType.HASH),
                        new KeySchemaElement().withAttributeName("SongTitle").withKeyType(KeyType.RANGE))
                .withBillingMode(BillingMode.PAY_PER_REQUEST)
                .withGlobalSecondaryIndexes(new GlobalSecondaryIndex()
                        .withIndexName(indexName)
                        .withKeySchema(new KeySchemaElement()
                                .withAttributeName("SongTitle")
                                .withKeyType(KeyType.HASH))
                        .withProjection(new Projection().withProjectionType(ProjectionType.ALL)))
                .withStreamSpecification(new StreamSpecification()
                        .withStreamEnabled(true)
                        .withStreamViewType(StreamViewType.NEW_AND_OLD_IMAGES)));

        LOGGER.info("Waiting for ACTIVE table status .....");
        ddbClient.waiters().tableExists().run(new WaiterParameters<>(new DescribeTableRequest(tableName)));


        LOGGER.info("Testing parameters for adding a new Replica in " + destRegion + " .....");

        CreateReplicationGroupMemberAction createReplicaAction = new CreateReplicationGroupMemberAction()
                .withRegionName(destRegion.getName())
                .withGlobalSecondaryIndexes(new ReplicaGlobalSecondaryIndex()
                        .withIndexName(indexName)
                        .withProvisionedThroughputOverride(new ProvisionedThroughputOverride()
                                .withReadCapacityUnits(15L)));


        ddbClient.updateTable(new UpdateTableRequest()
                .withTableName(tableName)
                .withReplicaUpdates(new ReplicationGroupUpdate()
                        .withCreate(createReplicaAction.withKMSMasterKeyId(null))));




    }
}
```