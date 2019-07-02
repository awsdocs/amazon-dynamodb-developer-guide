# AWS Management Console<a name="getting-started-step-2-Console"></a>

Follow these steps to write data to the `Music` table using the DynamoDB console\.

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. In the navigation pane on the left side of the console, choose **Tables**\. 

1. In the table list, choose the **Music** table\.

1. Choose the **Items** tab for the **Music** table\.

1. On the **Items** tab, choose **Create item**\.  
![\[Console screenshot showing the Music table and the Create item button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/GettingStarted/CreateItems.png)

1. Choose the plus sign \(**\+**\) symbol next to **SongTitle**\.

1. Choose **Append**, and then choose **Number**\. Name the field **Awards**\.  
![\[Console screenshot showing the Append list.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/GettingStarted/CreateItemsAddNumber.png)

1. Repeat this process to create an **AlbumTitle** of type **String**\.

1. Choose the following values for your item:

   1. For **Artist**, enter **No One You Know** as the value\.

   1. For **SongTitle**, enter **Call Me Today**\.

   1. For **AlbumTitle**, enter **Somewhat Famous**\.

   1. For **Awards**, enter **1**\.

1. Choose **Save**\.  
![\[Console screenshot showing the completed fields for the item.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/GettingStarted/CreateItemsDetails.png)

1. Repeat this process and create another item with the following values:

   1. For **Artist**, enter **Acme Band**\.

   1. For **SongTitle** enter **Happy Day**\.

   1. For **AlbumTitle**, enter **Songs About Life**\.

   1. For **Awards**, enter **10**\.