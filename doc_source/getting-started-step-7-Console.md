# AWS Management Console<a name="getting-started-step-7-Console"></a>

Follow these steps to use the DynamoDB console to query data through the `AlbumTitle-index` global secondary index\.

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. In the navigation pane on the left side of the console, choose **Tables**\.

1. Choose the **Music** table from the table list\.

1. Choose the **Items** tab for the Music table\.

1. In the drop\-down list, choose **Query**\.  
![\[Console screenshot showing the dropdown list with Query selected on the Items tab.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/GettingStarted/QueryClickQuery.png)

1. In the drop\-down list next to **Query** , choose **\[Index\] AlbumTitle\-index: AlbumTitle **\.

   For **AlbumTitle**, enter **Somewhat Famous**, and then choose **Start search**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/GettingStarted/GSISelectIndex.png)