# AWS Management Console<a name="getting-started-step-6-Console"></a>

To use the Amazon DynamoDB console to create a global secondary index `AlbumTitle-index` for the `Music` table:

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. In the navigation pane on the left side of the console, choose **Tables**\. 

1. Choose the **Music** table from the table list\.

1. Choose the **Indexes** tab for the Music table\.

1. Choose **Create index**\.  
![\[Console screenshot showing the create index button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/GettingStarted/CreateGSI.png)

1. For the **Partition key**, enter **AlbumTitle**, and then choose **Create index**\.  
![\[Console screenshot showing the completed fields in the create index dialog box.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/GettingStarted/CreateGSIDetails.png)