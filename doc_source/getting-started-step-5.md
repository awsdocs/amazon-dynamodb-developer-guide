# Step 5: Query data in a table<a name="getting-started-step-5"></a>

In this step, you query the data that you wrote to the `Music` table in [Step 2: Write data to a table using the console or AWS CLI](getting-started-step-2.md) by specifying `Artist`\. This will display all songs that are associated with the partition key: `Artist`\.

For more information about query operations, see [Query operations in DynamoDB](Query.md)\. 

## AWS Management Console<a name="getting-started-step-5-Console"></a>

Follow these steps to use the DynamoDB console to query data in the `Music` table\.

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. In the navigation pane on the left side of the console, choose **Tables**\.

1. Choose the **Music** table from the table list\.

1. Choose **View items**\.

1. Choose **Query**\.

1. For **Partition key**, enter **Acme Band**, and then choose **Run**\.  
![\[Console screenshot showing the completed query fields for the Music table.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/GettingStarted/QueryClickSearch.png)

## AWS CLI<a name="getting-started-step-5-CLI"></a>

The following AWS CLI example queries an item in the `Music` table\. You can do this either through the DynamoDB API or [PartiQL](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ql-reference.html), a SQL\-compatible query language for DynamoDB\.

------
#### [ DynamoDB API ]

You query an item through the DynamoDB API by using `query` and providing the partition key\.

```
aws dynamodb query \
    --table-name Music \
    --key-condition-expression "Artist = :name" \
    --expression-attribute-values  '{":name":{"S":"Acme Band"}}'
```

Using `query` returns all the songs associated with this particular `Artist`\.

```
{
    "Items": [
        {
            "AlbumTitle": {
                "S": "Updated Album Title"
            },
            "Awards": {
                "N": "10"
            },
            "Artist": {
                "S": "Acme Band"
            },
            "SongTitle": {
                "S": "Happy Day"
            }
        },
        {
            "AlbumTitle": {
                "S": "Another Album Title"
            },
            "Awards": {
                "N": "8"
            },
            "Artist": {
                "S": "Acme Band"
            },
            "SongTitle": {
                "S": "PartiQL Rocks"
            }
        }
    ],
    "Count": 2,
    "ScannedCount": 2,
    "ConsumedCapacity": null
}
```

------
#### [ PartiQL for DynamoDB ]

You query an item through PartiQL by using the `Select` statement and providing the partition key\.

```
aws dynamodb execute-statement --statement "SELECT * FROM Music   \
                                            WHERE Artist='Acme Band'"
```

Using the `Select` statement in this way returns all the songs associated with this particular `Artist`\.

```
{
    "Items": [
        {
            "AlbumTitle": {
                "S": "Updated Album Title"
            },
            "Awards": {
                "S": "10"
            },
            "Artist": {
                "S": "Acme Band"
            },
            "SongTitle": {
                "S": "Happy Day"
            }
        }
    ]
}
```

For more information about querying data with PartiQL, see [PartiQL select statements](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ql-reference.select.html)\.

------

To create a global secondary index for your table, proceed to [Step 6: Create a global secondary index](getting-started-step-6.md)\.