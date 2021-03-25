# AWS CLI<a name="getting-started-step-6-CLI"></a>

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