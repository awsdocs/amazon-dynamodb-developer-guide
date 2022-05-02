# AWS CLI<a name="getting-started-step-3-CLI"></a>

The following AWS CLI example reads an item from the `Music`\. You can do this either through the DynamoDB API or [PartiQL](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ql-reference.html), a SQL\-compatible query language for DynamoDB\.

------
#### [ DynamoDB API ]

**Note**  
The default behavior for DynamoDB is eventually consistent reads\. The `consistent-read` parameter is used below to demonstrate strongly consistent reads\.

```
aws dynamodb get-item --consistent-read \
    --table-name Music \
    --key '{ "Artist": {"S": "Acme Band"}, "SongTitle": {"S": "Happy Day"}}'
```

Using `get-item` returns the following sample result\.

```
{
    "Item": {
        "AlbumTitle": {
            "S": "Songs About Life"
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
aws dynamodb execute-statement --statement "SELECT * FROM Music   \
                                            WHERE Artist='Acme Band' AND SongTitle='Happy Day'"
```

Using the PartiQL `Select` statement returns the following sample result\.

```
{
    "Items": [
        {
            "AlbumTitle": {
                "S": "Songs About Life"
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
}
```

For more information about reading data with PartiQL, see [PartiQL Select Statements](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ql-reference.SELECT.html)\.

------