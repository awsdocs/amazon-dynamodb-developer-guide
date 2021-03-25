# Building API Operations<a name="workbench.querybuilder.operationbuilder.api"></a>

To use the NoSQL Workbench to build DynamoDB CRUD APIs choose **Interface\-based operations** at the top right corner of the NoSQL Workbench\.

Choose **Data plane operations** drop\-down, and then choose the operation that you want to build\.

![\[\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/QuerybuilderOperationBuilder.png)

You can perform the following operations in the operation builder\.

**Topics**
+ [Get Item](#workbench.querybuilder.operationbuilder.GetItem)
+ [Put Item](#workbench.querybuilder.operationbuilder.Put)
+ [Update Item](#workbench.querybuilder.operationbuilder.update)
+ [Delete Item](#workbench.querybuilder.operationbuilder.Delete)
+ [Query](#workbench.querybuilder.operationbuilder.Query)
+ [Scan](#workbench.querybuilder.operationbuilder.scan)
+ [TransactGetItems](#workbench.querybuilder.operationbuilder.transactget)
+ [TransactWriteItems](#workbench.querybuilder.operationbuilder.transactwrite)

## Get Item<a name="workbench.querybuilder.operationbuilder.GetItem"></a>

To run or generate code for a `Get Item` operation, do the following\.

1. Specify the partition key value\.

1. If you want to add a projection expression , do the following:

   1. Choose **Projection expression**\.

   1. Choose the **\+** \(plus sign\) next to **Projection expression**\.

   1. Specify the **Attribute name**,\.   
![\[Console screenshot showing the putitem operation builder.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/QueryBuilderGetItem.png)

1. If you want to generate code, choose **Generate code**\.

   Select your desired language from the displayed tabs\. You can now copy this code and use it in your application\.

1. If you want the operation to be run immediately, choose **Run**\.

For more information about this operation, see [PutItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_GetItem.html) in the *Amazon DynamoDB API Reference*\. 

## Put Item<a name="workbench.querybuilder.operationbuilder.Put"></a>

To run or generate code for a `Put Item` operation, do the following\.

1. Specify the partition key value\.

1. Specify the sort key value, if one exists\.

1. If you want to add non\-key attributes, do the following:

   1. Choose the **\+** \(plus sign\) next to **Other attributes**\.

   1. Specify the **Attribute name**, **Type**, and **Value**\. 

1. If a condition expression must be satisfied for the `Put Item` operation to succeed, do the following:

   1. Choose **Condition**\.

   1. Specify the attribute name, comparison operator, attribute type, and attribute value\.

   1. If other conditions are needed, choose **Condition** again\.

   For more information, see [Condition Expressions](Expressions.ConditionExpressions.md)\.  
![\[Console screenshot showing the putitem operation builder.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/QueryBuilderPutItem.png)

1. If you want to generate code, choose **Generate code**\.

   Select your desired language from the displayed tabs\. You can now copy this code and use it in your application\.

1. If you want the operation to be run immediately, choose **Run**\.

For more information about this operation, see [PutItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_PutItem.html) in the *Amazon DynamoDB API Reference*\. 

## Update Item<a name="workbench.querybuilder.operationbuilder.update"></a>

To run or generate code for an `Update Item` operation, do the following:

1. Enter the partition key value\.

1. Enter the sort key value, if one exists\.

1. In **Update expression**, choose the expression in the list\.

1. Choose the **\+** \(plus sign\) for the expression\.

1. Enter the attribute name and attribute value for the selected expression\.

1. If you want to add more expressions, choose another expression in the **Update Expression** drop\-down list, and then select the **\+**\.

1. If a condition expression must be satisfied for the `Update Item` operation to succeed, do the following:

   1. Choose **Condition**\.

   1. Specify the attribute name, comparison operator, attribute type, and attribute value\.

   1. If other conditions are needed, choose **Condition** again\.

   For more information, see [Condition Expressions](Expressions.ConditionExpressions.md)\.  
![\[Console screenshot showing the updateitem operation builder.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/QueryBuilderUpdateItem.png)

1. If you want to generate code, choose **Generate code**\.

   Choose the tab for the language that you want\. You can now copy this code and use it in your application\.

1. If you want the operation to be run immediately, choose **Run**\.

For more information about this operation, see [UpdateItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_UpdateItem.html) in the *Amazon DynamoDB API Reference*\. 

## Delete Item<a name="workbench.querybuilder.operationbuilder.Delete"></a>

To run or generate code for a `Delete Item` operation, do the following\.

1. Enter the partition key value\.

1. Enter the sort key value, if one exists\.

1. If a condition expression must be satisfied for the `Delete Item` operation to succeed, do the following:

   1. Choose **Condition**\.

   1. Specify the attribute name, comparison operator, attribute type, and attribute value\.

   1. If other conditions are needed, choose **Condition** again\.

   For more information, see [Condition Expressions](Expressions.ConditionExpressions.md)\.  
![\[Console screenshot showing the deleteitem operation builder.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/QueryBuilderDeleteItem.png)

1. If you want to generate code, choose **Generate code**\.

   Choose the tab for the language that you want\. You can now copy this code and use it in your application\.

1. If you want the operation to be run immediately, choose **Run**\.

For more information about this operation, see [DeleteItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DeleteItem.html) in the *Amazon DynamoDB API Reference*\.

## Query<a name="workbench.querybuilder.operationbuilder.Query"></a>

To run or generate code for a `Query` operation, do the following\.

1. Specify the partition key value\.

1. If a sort key is needed for the `Query` operation:

   1. Select **Sort key**\.

   1. Specify the comparison operator, attribute type, and attribute value\.

1. If not all the attributes should be returned with the operation result, select **Projection expression**\.

1. Choose the **\+** \(plus sign\)\.

1. Enter the attribute to return with the query result\.

1. If more attributes are needed, choose the **\+ **\.

1. If a condition expression must be satisfied for the `Query` operation to succeed, do the following:

   1. Choose **Condition**\.

   1. Specify the attribute name, comparison operator, attribute type, and attribute value\.

   1. If other conditions are needed, choose **Condition** again\.

   For more information, see [Condition Expressions](Expressions.ConditionExpressions.md)\.  
![\[Console screenshot showing the query operation builder.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/QueryBuilderQuery.png)

1. If you want to generate code, choose **Generate code**\.

   Choose the tab for the language that you want\. You can now copy this code and use it in your application\.

1. If you want the operation to be run immediately, choose **Run**\.

For more information about this operation, see [Query](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Query.html) in the *Amazon DynamoDB API Reference*\. 

## Scan<a name="workbench.querybuilder.operationbuilder.scan"></a>

To run or generate code for a `Scan` operation, do the following\.

1. If not all the attributes should be returned with the operation result, select **Projection expression**\.

1. Choose the **\+** \(plus sign\)\.

1. Specify the attribute to return with the query result\.

1. If more attributes are needed, choose the **\+ ** again\.

1. If a condition expression must be satisfied for the scan operation to succeed, do the following:

   1. Choose **Condition**\.

   1. Specify the attribute name, comparison operator, attribute type, and attribute value\.

   1. If other conditions are needed, choose **Condition** again\. 

   For more information, see [Condition Expressions](Expressions.ConditionExpressions.md)\.

1. If you want to generate code, choose **Generate code**\.

   Choose the tab for the language that you want\. You can now copy this code and use it in your application\.

1. If you want the operation to be run immediately, choose **Run**\.

## TransactGetItems<a name="workbench.querybuilder.operationbuilder.transactget"></a>

To run or generate code for a `TransactGetItems` operation, do the following\.

1. Choose the **\+** \(plus sign\)\.

1. Follow the instructions for the [Get Item](#workbench.querybuilder.operationbuilder.GetItem) operation\. 

   When you are done specifying the details of the operation, choose the **\+** \(plus sign\) if you want to add more operations\.  
![\[Console screenshot showing the plus sign button in transactgetitems operation builder.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/QueryBuilderTransactgetitemoperation.png)

   To change the order of actions, choose an action in the list on the left side, and then choose the up or down arrows to move it up or down in the list\. 

   To delete an action, choose the action in the list, and then choose the **Delete** \(trash can\) icon\. 

1. If you want to generate code, choose **Generate code**\. 

   Choose the tab for the language that you want\. You can now copy this code and use it in your application\.

1. If you want the operation to be run immediately, choose **Run**\.

For more information about transactions, see [Amazon DynamoDB Transactions](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/transactions.html)\.

## TransactWriteItems<a name="workbench.querybuilder.operationbuilder.transactwrite"></a>

To run or generate code for a `TransactWriteItems` operation, do the following\.

1. In the **Actions** drop\-down list, choose the operation that you want\.
   + For `DeleteItem`, follow the instructions for the [Delete Item](#workbench.querybuilder.operationbuilder.Delete) operation\. 
   + For `PutItem`, follow the instructions for the [Put Item](#workbench.querybuilder.operationbuilder.Put) operation\. 
   + For `UpdateItem`, follow the instructions for the [Update Item](#workbench.querybuilder.operationbuilder.update) operation\.

   When you are done specifying the details of the operation, choose the **\+** button\.  
![\[Console screenshot showing the plus sign button in transactwriteitems operation builder.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/QueryBuilderTransactWriteitemoperation.png)

   To change the order of actions, choose an action in the list on the left side, and then choose the up or down arrows to move it up or down in the list\. 

   To delete an action, choose the action in the list, and then choose the **Delete** \(trash can\) icon\.   
![\[Console screenshot showing the up and down arrows and trash can buttons in operation builder.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/QueryBuilderTransactWriteitemreorder.png)

1. If you want to generate code, choose **Generate code**\. 

   Choose the tab for the language that you want\. You can now copy this code and use it in your application\.

1. If you want the operation to be run immediately, choose **Run**\.

For more information about transactions, see [Amazon DynamoDB Transactions](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/transactions.html)\.