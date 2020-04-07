# AWS Management Console<a name="getting-started-step-4-Console"></a>

You can use the DynamoDB console to update data in the `Music` table\.

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. In the navigation pane on the left side of the console, choose **Tables**\.

1. Choose the **Music** table from the table list\.

1. Choose the **Items** tab for the `Music` table\.

1. Choose the item whose `Artist` value is **Acme Band** and `SongTitle` value is **Happy Day**\.

1. Update the **AlbumTitle** value to **Updated Album Title**, and then choose **Save**\.

The following image shows the updated item on the console\.

![\[Console screenshot showing the Edit item page with the updated album title highlighted.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/GettingStarted/UpdateItem.png)