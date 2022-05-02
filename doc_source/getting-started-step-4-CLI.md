# AWS CLI<a name="getting-started-step-4-CLI"></a>

The following AWS CLI example updates an item in the `Music` table\. You can do this either through the DynamoDB API or [PartiQL](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ql-reference.html), a SQL\-compatible query language for DynamoDB\.

------
#### [ DynamoDB API ]

```
aws dynamodb update-item \
    --table-name Music \
    --key '{ "Artist": {"S": "Acme Band"}, "SongTitle": {"S": "Happy Day"}}' \
    --update-expression "SET AlbumTitle = :newval" \
    --expression-attribute-values '{":newval":{"S":"Updated Album Title"}}' \
    --return-values ALL_NEW
```

Using `update-item` returns the following sample result because `return-values ALL_NEW` was specified\.

```
{
    "Attributes": {
        "AlbumTitle": {
            "S": "Updated Album Title"
        },
        "Awards": {
            "N": "10"
        },
        "SongTitle": {
            "S": "Happy Day"
        },
        "Artist": {
            "S": "Acme Band"
        }
    }
}
```

------
#### [ PartiQL for DynamoDB ]

```
aws dynamodb execute-statement --statement "UPDATE Music  \
                                            SET AlbumTitle='Updated Album Title'  \
                                            WHERE Artist='Acme Band' AND SongTitle='Happy Day' \
                                            RETURNING ALL NEW *"
```

Using the `Update` statement returns the following sample result because `RETURNING ALL NEW *` was specified\.

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

For more information about updating data with PartiQL, see [PartiQL Update Statements](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ql-reference.update.html)\.

------