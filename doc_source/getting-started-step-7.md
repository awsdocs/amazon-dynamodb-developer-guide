# Step 7: Query the global secondary index<a name="getting-started-step-7"></a>

In this step, you query a global secondary index on the `Music` table using the Amazon DynamoDB console or the AWS CLI\.

For more information about global secondary indexes, see [Using Global Secondary Indexes in DynamoDB](GSI.md)\.

## AWS Management Console<a name="getting-started-step-7-Console"></a>

Follow these steps to use the DynamoDB console to query data through the `AlbumTitle-index` global secondary index\.

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. In the navigation pane on the left side of the console, choose **Tables**\.

1. Choose the **Music** table from the table list\.

1. Select the **View items**\.

1. Choose **Query**\.

1. In the drop\-down list under **Query**, choose **AlbumTitle\-index**\.

   For **AlbumTitle**, enter **Somewhat Famous**, and then choose **Run**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/GettingStarted/GSISelectIndex.png)

## AWS CLI<a name="getting-started-step-7-CLI"></a>

The following AWS CLI example queries a global secondary index `AlbumTitle-index` on the `Music` table\. You can do this either through the DynamoDB API or [PartiQL](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ql-reference.html), a SQL\-compatible query language for DynamoDB\.

------
#### [ DynamoDB API ]

You query the global secondary index through the DynamoDB API by using `query` and providing the index name\.

```
aws dynamodb query \
    --table-name Music \
    --index-name AlbumTitle-index \
    --key-condition-expression "AlbumTitle = :name" \
    --expression-attribute-values  '{":name":{"S":"Somewhat Famous"}}'
```

Using `query` returns the following sample result\.

```
{
    "Items": [
        {
            "AlbumTitle": {
                "S": "Somewhat Famous"
            },
            "Awards": {
                "S": "1"
            },
            "Artist": {
                "S": "No One You Know"
            },
            "SongTitle": {
                "S": "Call Me Today"
            }
        },
        {
            "AlbumTitle": {
                "S": "Somewhat Famous"
            },
            "Awards": {
                "N": "2"
            },
            "Artist": {
                "S": "No One You Know"
            },
            "SongTitle": {
                "S": "Howdy"
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

You query the global secondary index through PartiQL by using the `Select` statement and providing the index name\.

**Note**  
You'll need to escape the double quotes around `Music` and `AlbumTitle-index` since you're doing this through the CLI\.

```
aws dynamodb execute-statement --statement "SELECT * FROM \"Music\".\"AlbumTitle-index\"  \
                                            WHERE AlbumTitle='Somewhat Famous'"
```

Using the `Select` statement in this way returns the following sample result\.

```
{
    "Items": [
        {
            "AlbumTitle": {
                "S": "Somewhat Famous"
            },
            "Awards": {
                "S": "1"
            },
            "Artist": {
                "S": "No One You Know"
            },
            "SongTitle": {
                "S": "Call Me Today"
            }
        },
        {
            "AlbumTitle": {
                "S": "Somewhat Famous"
            },
            "Awards": {
                "S": "2"
            },
            "Artist": {
                "S": "No One You Know"
            },
            "SongTitle": {
                "S": "Howdy"
            }
        }
    ]
}
```

For more information about querying data with PartiQL, see [PartiQL select statements](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ql-reference.select.html)\.

------