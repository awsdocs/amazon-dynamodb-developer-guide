# AWS CLI<a name="getting-started-step-7-CLI"></a>

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

For more information about querying data with PartiQL, see [PartiQL Select Statements](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ql-reference.select.html)\.

------