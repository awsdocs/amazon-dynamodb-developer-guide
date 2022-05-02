# AWS CLI<a name="getting-started-step-1-CLI"></a>

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