# AWS CLI<a name="getting-started-step-5-CLI"></a>

The following AWS CLI example queries an item in the `Music` table using `query`\.

```
aws dynamodb query \
    --table-name Music \
    --key-condition-expression "Artist = :name" \
    --expression-attribute-values  '{":name":{"S":"Acme Band"}}'
```

Using `query` returns the following sample result\.

```
{
    "Count": 1,
    "Items": [
        {
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
    ],
    "ScannedCount": 1,
    "ConsumedCapacity": null
}
```