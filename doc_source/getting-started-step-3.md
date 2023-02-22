# Step 3: Read data from a table<a name="getting-started-step-3"></a>

In this step, you will read back an item that was created in [Step 2: Write data to a table using the console or AWS CLI](getting-started-step-2.md)\. You can use the DynamoDB console or the AWS CLI to read an item from the `Music` table by specifying `Artist` and `SongTitle`\. 

For more information about read operations in DynamoDB, see [Reading an item](WorkingWithItems.md#WorkingWithItems.ReadingData)\. 

## AWS Management Console<a name="getting-started-step-3-Console"></a>

Follow these steps to read data from the `Music` table using the DynamoDB console\.

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. In the navigation pane on the left side of the console, choose **Tables**\. 

1. Choose the **Music** table from the table list\.

1. Select the **View items**\.

1. On the **Items** tab, view the list of items stored in the table, sorted by `Artist` and `SongTitle`\. The first item in the list is the one with the `Artist` **Acme Band** and the `SongTitle` **Happy Day**\.  
![\[Console screenshot showing the items list with Acme Band at the top of the list.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/GettingStarted/GetItems.png)

## AWS CLI<a name="getting-started-step-3-CLI"></a>

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

For more information about reading data with PartiQL, see [PartiQL select statements](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ql-reference.SELECT.html)\.

------

To update the data in your table, proceed to [Step 4: Update data in a table](getting-started-step-4.md)\.