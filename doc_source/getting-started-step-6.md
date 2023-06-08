# Step 6: Create a global secondary index<a name="getting-started-step-6"></a>

In this step, you create a global secondary index for the `Music` table that you created in [Step 1: Create a table](getting-started-step-1.md)\.

For more information about global secondary indexes, see [Using Global Secondary Indexes in DynamoDB](GSI.md)\. 

## AWS Management Console<a name="getting-started-step-6-Console"></a>

To use the Amazon DynamoDB console to create a global secondary index `AlbumTitle-index` for the `Music` table:

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. In the navigation pane on the left side of the console, choose **Tables**\. 

1. Choose the **Music** table from the table list\.

1. Choose the **Indexes** tab for the Music table\.

1. Choose **Create index**\.  
![\[Console screenshot showing the create index button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/GettingStarted/CreateGSI.png)

1. For the **Partition key**, enter **AlbumTitle**\.

1. For **Index name**, enter **AlbumTitle\-index**\.

1. Leave the other settings on their default values and choose **Create index**\.  
![\[Console screenshot showing the completed fields in the create index dialog box.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/GettingStarted/CreateGSIDetails.png)

## AWS CLI<a name="getting-started-step-6-CLI"></a>

The following AWS CLI example creates a global secondary index `AlbumTitle-index` for the `Music` table using `update-table`\.

```
aws dynamodb update-table \
    --table-name Music \
    --attribute-definitions AttributeName=AlbumTitle,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"AlbumTitle-index\",\"KeySchema\":[{\"AttributeName\":\"AlbumTitle\",\"KeyType\":\"HASH\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```

Using `update-table` returns the following sample result\.

```
{
    "TableDescription": {
        "TableArn": "arn:aws:dynamodb:us-west-2:522194210714:table/Music",
        "AttributeDefinitions": [
            {
                "AttributeName": "AlbumTitle",
                "AttributeType": "S"
            },
            {
                "AttributeName": "Artist",
                "AttributeType": "S"
            },
            {
                "AttributeName": "SongTitle",
                "AttributeType": "S"
            }
        ],
        "GlobalSecondaryIndexes": [
            {
                "IndexSizeBytes": 0,
                "IndexName": "AlbumTitle-index",
                "Projection": {
                    "ProjectionType": "ALL"
                },
                "ProvisionedThroughput": {
                    "NumberOfDecreasesToday": 0,
                    "WriteCapacityUnits": 5,
                    "ReadCapacityUnits": 10
                },
                "IndexStatus": "CREATING", 
                "Backfilling": false,
                "KeySchema": [
                    {
                        "KeyType": "HASH",
                        "AttributeName": "AlbumTitle"
                    }
                ],
                "IndexArn": "arn:aws:dynamodb:us-west-2:522194210714:table/Music/index/AlbumTitle-index",
                "ItemCount": 0
            }
        ],
        "ProvisionedThroughput": {
            "NumberOfDecreasesToday": 0,
            "WriteCapacityUnits": 5,
            "ReadCapacityUnits": 10
        },
        "TableSizeBytes": 0,
        "TableName": "Music",
        "TableStatus": "UPDATING",
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

Note that the value of the `IndexStatus` field is set to `CREATING`\.

To verify that DynamoDB has finished creating the `AlbumTitle-index` global secondary index, use the `describe-table` command\. 

```
 aws dynamodb describe-table --table-name Music | grep IndexStatus
```

This command returns the following result\. The index is ready for use when the value of the `IndexStatus` field returned is set to `ACTIVE`\. 

```
"IndexStatus": "ACTIVE",
```

Next, you can query the global secondary index\. For details, see [Step 7: Query the global secondary index](getting-started-step-7.md)\.