# Step 1: Create a table<a name="getting-started-step-1"></a>

In this step, you create a `Music` table in Amazon DynamoDB\. The table has the following details:
+ Partition key — `Artist`
+ Sort key — `SongTitle`

For more information about table operations, see [Working with tables and data in DynamoDB](WorkingWithTables.md)\. 

**Note**  
Before you begin, make sure that you followed the steps in [Prerequisites \- getting started tutorial](GettingStarted.SettingUp.DynamoWebService.md)\.

## AWS Management Console<a name="getting-started-step-1-Console"></a>

To create a new `Music` table using the DynamoDB console:

1. Sign in to the AWS Management Console and open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. In the navigation pane on the left side of the console, choose **Dashboard**\. 

1. On the right side of the console, choose **Create Table**\.  
![\[Console screenshot showing the Create table button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/GettingStarted/CreateTableDashboard.png)

1. Enter the table details as follows:

   1. For the table name, enter **Music**\.

   1. For the partition key, enter **Artist**\.

   1. Enter **SongTitle** as the sort key\.

   1. Leave **Default settings** selected\.

1. Choose **Create** to create the table\.  
![\[Console screenshot showing the Create table page with fields completed.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/GettingStarted/CreateTableMusic.png)

## AWS CLI<a name="getting-started-step-1-CLI"></a>

The following AWS CLI example creates a new `Music` table using `create-table`\.

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
        ReadCapacityUnits=5,WriteCapacityUnits=5 \
    --table-class STANDARD
```

Using `create-table` returns the following sample result\.

```
{

    "TableDescription": {
        "TableArn": "arn:aws:dynamodb:us-west-2:522194210714:table/Music",
        "AttributeDefinitions": [
            {
                "AttributeName": "Artist",
                "AttributeType": "S"
            },
            {
                "AttributeName": "SongTitle",
                "AttributeType": "S"
            }
        ],
        "ProvisionedThroughput": {
            "NumberOfDecreasesToday": 0,
            "WriteCapacityUnits": 5,
            "ReadCapacityUnits": 5
        },
        "TableClassSummary": {
            "LastUpdateDateTime": 1558028402.69,
            "TableClass": "STANDARD"
        },
        "TableSizeBytes": 0,
        "TableName": "Music",
        "TableStatus": "CREATING", 
        "TableId": "d04c7240-0e46-435d-b231-d54091fe1017",
        "KeySchema": [
            {
                "KeyType": "HASH",
                "AttributeName": "Artist"
            },
            {
                "KeyType": "RANGE",
                "AttributeName": "SongTitle"
            }
        ],
        "ItemCount": 0,
        "CreationDateTime": 1558028402.69
    }
}
```

Note that the value of the `TableStatus` field is set to `CREATING`\.

To verify that DynamoDB has finished creating the `Music` table, use the `describe-table` command\. 

```
 aws dynamodb describe-table --table-name Music | grep TableStatus
```

This command returns the following result\. When DynamoDB finishes creating the table, the value of the `TableStatus` field is set to `ACTIVE`\. 

```
"TableStatus": "ACTIVE",
```

Once the table is in `ACTIVE` status, it's considered best practice to enable [Point\-in\-time recovery for DynamoDB](PointInTimeRecovery.md) on the table by running the following command:

```
aws dynamodb update-continuous-backups \ 
    --table-name Music \ 
    --point-in-time-recovery-specification \ 
        PointInTimeRecoveryEnabled=true
```

**Note**  
There are cost implications to enabling continuous backups with point\-in\-time recovery\. For more information about pricing, see [Amazon DynamoDB pricing](https://aws.amazon.com/dynamodb/pricing)\.

After creating the new table, proceed to [Step 2: Write data to a table using the console or AWS CLI](getting-started-step-2.md)\.