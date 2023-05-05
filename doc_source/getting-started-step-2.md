# Step 2: Write data to a table using the console or AWS CLI<a name="getting-started-step-2"></a>

In this step, you insert several items into the `Music` table that you created in [Step 1: Create a table](getting-started-step-1.md)\. 

For more information about write operations, see [Writing an item](WorkingWithItems.md#WorkingWithItems.WritingData)\.

## AWS Management Console<a name="getting-started-step-2-Console"></a>

Follow these steps to write data to the `Music` table using the DynamoDB console\.

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. In the navigation pane on the left side of the console, choose **Tables**\. 

1. In the table list, choose the **Music** table\.  
![\[Console screenshot showing the Tables view with a Music table.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/GettingStarted/tables.music.png)

1. Select **Explore Table Items**\.  
![\[Console screenshot showing the explore table items button in the table view.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/GettingStarted/view-items.png)

1. In the **Items** view, choose **Create item**\.  
![\[Console screenshot showing the Music table and the Create item button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/GettingStarted/CreateItems.png)

1. Choose **Add new attribute**, and then choose **Number**\. Name the field **Awards**\.  
![\[Console screenshot showing the Add new attribute list.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/GettingStarted/CreateItemsAddNumber.png)

1. Repeat this process to create an **AlbumTitle** of type **String**\.

1. Enter the following values for your item:

   1. For **Artist**, enter **No One You Know** as the value\.

   1. For **SongTitle**, enter **Call Me Today**\.

   1. For **AlbumTitle**, enter **Somewhat Famous**\.

   1. For **Awards**, enter **1**\.

1. Choose **Create item**\.  
![\[Console screenshot showing the completed fields for the item.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/GettingStarted/CreateItemsDetails.png)

1. Repeat this process and create another item with the following values:

   1. For **Artist**, enter **Acme Band**\.

   1. For **SongTitle** enter **Happy Day**\.

   1. For **AlbumTitle**, enter **Songs About Life**\.

   1. For **Awards**, enter **10**\.

1. Do this one more time to create another item with the same **Artist** as the previous step, but different values for the other attributes:

   1. For **Artist**, enter **Acme Band**\.

   1. For **SongTitle** enter **PartiQL Rocks**\.

   1. For **AlbumTitle**, enter **Another Album Title**\.

   1. For **Awards**, enter **8**\.

## AWS CLI<a name="getting-started-step-2-CLI"></a>

The following AWS CLI example creates several new items in the `Music` table\. You can do this either through the DynamoDB API or [PartiQL](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ql-reference.html), a SQL\-compatible query language for DynamoDB\.

------
#### [ DynamoDB API ]

```
aws dynamodb put-item \
    --table-name Music  \
    --item \
        '{"Artist": {"S": "No One You Know"}, "SongTitle": {"S": "Call Me Today"}, "AlbumTitle": {"S": "Somewhat Famous"}, "Awards": {"N": "1"}}'

aws dynamodb put-item \
    --table-name Music  \
    --item \
        '{"Artist": {"S": "No One You Know"}, "SongTitle": {"S": "Howdy"}, "AlbumTitle": {"S": "Somewhat Famous"}, "Awards": {"N": "2"}}'

aws dynamodb put-item \
    --table-name Music \
    --item \
        '{"Artist": {"S": "Acme Band"}, "SongTitle": {"S": "Happy Day"}, "AlbumTitle": {"S": "Songs About Life"}, "Awards": {"N": "10"} }'
                            
aws dynamodb put-item \
    --table-name Music \
    --item \
        '{"Artist": {"S": "Acme Band"}, "SongTitle": {"S": "PartiQL Rocks"}, "AlbumTitle": {"S": "Another Album Title"}, "Awards": {"N": "8"} }'
```

------
#### [ PartiQL for DynamoDB ]

```
aws dynamodb execute-statement --statement "INSERT INTO Music  \
                VALUE  \
                {'Artist':'No One You Know','SongTitle':'Call Me Today', 'AlbumTitle':'Somewhat Famous', 'Awards':'1'}"

aws dynamodb execute-statement --statement "INSERT INTO Music  \
                VALUE  \
                {'Artist':'No One You Know','SongTitle':'Howdy', 'AlbumTitle':'Somewhat Famous', 'Awards':'2'}"

aws dynamodb execute-statement --statement "INSERT INTO Music  \
                VALUE  \
                {'Artist':'Acme Band','SongTitle':'Happy Day', 'AlbumTitle':'Songs About Life', 'Awards':'10'}"
                            
aws dynamodb execute-statement --statement "INSERT INTO Music  \
                VALUE  \
                {'Artist':'Acme Band','SongTitle':'PartiQL Rocks', 'AlbumTitle':'Another Album Title', 'Awards':'8'}"
```

For more information about writing data with PartiQL, see [PartiQL insert statements](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ql-reference.insert.html)\.

------

For more information about supported data types in DynamoDB, see [Data types](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.NamingRulesDataTypes.html#HowItWorks.DataTypes)\. 

For more information about how to represent DynamoDB data types in JSON, see [Attribute values](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_AttributeValue.html)\. 

After writing data to your table, proceed to [Step 3: Read data from a table](getting-started-step-3.md)\.