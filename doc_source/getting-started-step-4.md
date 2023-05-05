# Step 4: Update data in a table<a name="getting-started-step-4"></a>

In this step, you update an item that you created in [Step 2: Write data to a table using the console or AWS CLI](getting-started-step-2.md)\. You can use the DynamoDB console or the AWS CLI to update the `AlbumTitle` of an item in the `Music` table by specifying `Artist`, `SongTitle`, and the updated `AlbumTitle`\. 

For more information about write operations, see [Writing an item](WorkingWithItems.md#WorkingWithItems.WritingData)\. 

## AWS Management Console<a name="getting-started-step-4-Console"></a>

You can use the DynamoDB console to update data in the `Music` table\.

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. In the navigation pane on the left side of the console, choose **Tables**\.

1. Choose the **Music** table from the table list\.

1. Choose **View items**\.

1. Choose the item whose `Artist` value is **Acme Band** and `SongTitle` value is **Happy Day**\.

1. Update the **AlbumTitle** value to **Updated Album Title**, and then choose **Save**\.

The following image shows the updated item on the console\.

![\[Console screenshot showing the Edit item page with the updated album title highlighted.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/GettingStarted/UpdateItem.png)

## AWS CLI<a name="getting-started-step-4-CLI"></a>

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

For more information about updating data with PartiQL, see [PartiQL update statements](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ql-reference.update.html)\.

------

To query the data in the `Music` table, proceed to [Step 5: Query data in a table](getting-started-step-5.md)\.