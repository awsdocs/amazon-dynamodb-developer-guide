# Step 3: Query the data<a name="SampleData.Query"></a>

**Topics**

In this step, you try some simple queries against the tables that you created, using the Amazon DynamoDB console\.

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. In the navigation pane, choose **Tables**\.

1. In the list of tables, choose **Reply**\.

1. Choose the **Items** tab to view the data that you loaded into the table\.

1. Choose the data filtering link, located just below the **Create item** button\.  
![\[Console screenshot showing the data filtering link.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/console-data-filtering-01.png)![\[Console screenshot showing the data filtering link.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Console screenshot showing the data filtering link.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

   When you choose the link, the console reveals a data filtering pane\.  
![\[Console screenshot showing the data filtering pane.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/console-data-filtering-02.png)![\[Console screenshot showing the data filtering pane.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Console screenshot showing the data filtering pane.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

1. In the data filtering pane, do the following:

   1. Change the operation from **Scan** to **Query**\.

   1. For the **Partition key**, enter the value **Amazon DynamoDB\#DynamoDB Thread 1**\.

   1. Choose **Start**\. Only the items that match your query criteria are returned from the `Reply` table\.

1. The `Reply` table has a global secondary index on the `PostedBy` and `Message` attributes\. You can use the data filtering pane to query the index\. Do the following:

   1. Change the query source from the following:

      `[Table] Reply: Id, ReplyDateTime`

      To the following:

      `[Index] PostedBy-Message-Index: PostedBy, Message`

   1. For the **Partition key**, enter the value **User A**\.

   1. Choose **Start**\. Only the items that match your query criteria are returned from `PostedBy-Message-Index`\.

Take some time to explore your other tables using the DynamoDB console:
+ `ProductCatalog`
+ `Forum`
+ `Thread`