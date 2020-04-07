# AWS Management Console<a name="getting-started-step-5-Console"></a>

Follow these steps to use the DynamoDB console to query data in the `Music` table\.

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. In the navigation pane on the left side of the console, choose **Tables**\.

1. Choose the **Music** table from the table list\.

1. Choose the **Items** tab for the `Music` table\.

1. In the drop\-down list, choose **Query**\.  
![\[Console screenshot showing the dropdown list with Query selected on the Items tab.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/GettingStarted/QueryClickQuery.png)

1. For **Partition key**, enter **Acme Band**, and then choose **Start search**\.  
![\[Console screenshot showing the completed query fields for the Music table.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/GettingStarted/QueryClickSearch.png)