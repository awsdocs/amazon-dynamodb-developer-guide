# Building PartiQL statements<a name="workbench.querybuilder.partiql"></a>

To use NoSQL Workbench to build [PartiQL for DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ql-reference.html) statements choose **PartiQL operations** at the top right corner of NoSQL Workbench\.

You can build the following PartiQL statement types in the operation builder\.

**Topics**
+ [Singleton statements](#workbench.querybuilder.partiql.single)
+ [Transactions](#workbench.querybuilder.partiql.transaction)
+ [Batch](#workbench.querybuilder.partiql.batch)

## Singleton statements<a name="workbench.querybuilder.partiql.single"></a>

To run or generate code for a PartiQL statement, do the following\.

![\[\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/partiql.single.png)

1. Choose **PartiQL statement**\.

1. Enter a valid [PartiQL statement](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ql-reference.statements.html)\.

1. If your statement uses parameters:

   1. Choose **Optional request parameters**\.

   1. Choose **Add new parameters**\.

   1. Enter the attribute type and value\.

   1. If you want to add additional parameters, repeat steps b and c\.

1. If you want to generate code, choose **Generate code**\.

   Select your desired language from the displayed tabs\. You can now copy this code and use it in your application\.

1. If you want the operation to be run immediately, choose **Run**\.

1. If you want to save this operation for later use, choose **Save operation**\. Then enter a name for your operation and choose **Save**\.

## Transactions<a name="workbench.querybuilder.partiql.transaction"></a>

To run or generate code for a PartiQL transaction, do the following\.

![\[\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/partiql.transaction.png)

1. Choose **PartiQL transaction**\.

1. Choose **Add a new statement**\.

1. Enter a valid [PartiQL statement](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ql-reference.statements.html)\.
**Note**  
Read and write operations are not supported in the same PartiQL transaction request\. A SELECT statement cannot be in the same request with INSERT, UPDATE, and DELETE statements\. See [Performing transactions with PartiQL for DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ql-reference.multiplestatements.transactions.html) for more details\.

1. If your statement uses parameters

   1. Choose **Optional request parameters**\.

   1. Choose **Add new parameters**\.

   1. Enter the attribute type and value\.

   1. If you want to add additional parameters, repeat steps b and c\.

1. If you want to add more statements, repeat steps 2 to 4\.

1. If you want to generate code, choose **Generate code**\.

   Select your desired language from the displayed tabs\. You can now copy this code and use it in your application\.

1. If you want the operation to be run immediately, choose **Run**\.

1. If you want to save this operation for later use, choose **Save operation**\. Then enter a name for your operation and choose **Save**\.

## Batch<a name="workbench.querybuilder.partiql.batch"></a>

To run or generate code for a PartiQL batch, do the following\.

![\[\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/partiql.batch.png)

1. Choose **PartiQL batch**\.

1. Choose **Add a new statement**\.

1. Enter a valid [PartiQL statement](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ql-reference.statements.html)\.
**Note**  
 Read and write operations are not supported in the same PartiQL batch request, which means a SELECT statement cannot be in the same request with INSERT, UPDATE, and DELETE statements\. Write operations to the same item are not allowed\. As with the BatchGetItem operation, only singleton read operations are supported\. Scan and query operations are not supported\. See [Running batch operations with PartiQL for DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ql-reference.multiplestatements.batching.html) for more details\.

1. If your statement uses parameters:

   1. Choose **Optional request parameters**\.

   1. Choose **Add new parameters**\.

   1. Enter the attribute type and value\.

   1. If you want to add additional parameters, repeat steps b and c\.

1. If you want to add more statements, repeat steps 2 to 4\.

1. If you want to generate code, choose **Generate code**\.

   Select your desired language from the displayed tabs\. You can now copy this code and use it in your application\.

1. If you want the operation to be run immediately, choose **Run**\.

1. If you want to save this operation for later use, choose **Save operation**\. Then enter a name for your operation and choose **Save**\.