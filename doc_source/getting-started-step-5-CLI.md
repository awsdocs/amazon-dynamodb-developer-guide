# AWS CLI<a name="getting-started-step-5-CLI"></a>

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

For more information about querying data with PartiQL, see [PartiQL Select Statements](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ql-reference.select.html)\.

------