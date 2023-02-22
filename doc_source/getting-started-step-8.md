# Step 8: \(Optional\) clean up resources<a name="getting-started-step-8"></a>

If you no longer need the Amazon DynamoDB table that you created for the tutorial, you can delete it\. This step helps ensure that you aren't charged for resources that you aren't using\. You can use the DynamoDB console or the AWS CLI to delete the `Music` table that you created in [Step 1: Create a table](getting-started-step-1.md)\.

For more information about table operations in DynamoDB, see [Working with tables and data in DynamoDB](WorkingWithTables.md)\. 

## AWS Management Console<a name="getting-started-step-8-Console"></a>

To delete the `Music` table using the console:

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. In the navigation pane on the left side of the console, choose **Tables**\.

1. Choose the **Music** table from the table list\.

1. Choose the **Indexes** tab for the `Music` table\.

1. Choose **Delete table**\.  
![\[Console screenshot showing the Delete table button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/GettingStarted/DeleteTable.png)

## AWS CLI<a name="getting-started-step-8-CLI"></a>

The following AWS CLI example deletes the `Music` table using `delete-table`\.

```
aws dynamodb delete-table --table-name Music
```