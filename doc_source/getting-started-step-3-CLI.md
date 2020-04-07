# AWS CLI<a name="getting-started-step-3-CLI"></a>

The following AWS CLI example reads an item from the `Music` table using `get-item`\.

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