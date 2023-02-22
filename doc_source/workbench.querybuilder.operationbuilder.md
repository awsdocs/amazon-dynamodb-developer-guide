# Building complex operations<a name="workbench.querybuilder.operationbuilder"></a>

The operation builder in NoSQL Workbench for Amazon DynamoDB provides a visual interface where you can perform complex data plane operations\. It includes support for projection expressions and condition expressions\. Once you've built an operation, you can save it for later use \(up to 50 operations can be saved\)\. You can then browse a list of your frequently used data\-plane operations in the **Saved Operations** menu, and use them to automatically populate and build a new operation\. You can also generate sample code for these operations, in multiple languages\.

NoSQL Workbench supports building [PartiQL](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ql-reference.html) for DynamoDB statements, which allows you to interact with DynamoDB using a SQL\-compatible query language\. NoSQL Workbench also supports building DynamoDB CRUD API operations\.

To use NoSQL Workbench to build operations, in the navigation pane on the left side, choose the **Operation builder** icon\.

![\[Console screenshot showing the operation builder icon.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/QueryBuilderChoose.png)

**Topics**
+ [Building PartiQL statements](workbench.querybuilder.partiql.md)
+ [Building API operations](workbench.querybuilder.operationbuilder.api.md)