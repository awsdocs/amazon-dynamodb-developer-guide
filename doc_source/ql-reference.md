# PartiQL \- a SQL\-compatible query language for Amazon DynamoDB<a name="ql-reference"></a>

Amazon DynamoDB supports [PartiQL](https://partiql.org/), a SQL\-compatible query language, to select, insert, update, and delete data in Amazon DynamoDB\. Using PartiQL, you can easily interact with DynamoDB tables and run ad hoc queries using the AWS Management Console, NoSQL Workbench, AWS Command Line Interface, and DynamoDB APIs for PartiQL\.

PartiQL operations provide the same availability, latency, and performance as the other DynamoDB data plane operations\.

The following sections describe the DynamoDB implementation of PartiQL\.

**Topics**
+ [What is PartiQL?](#ql-reference.what-is)
+ [PartiQL in Amazon DynamoDB](#ql-reference.what-is)
+ [Getting started](ql-gettingstarted.md)
+ [Data types](ql-reference.data-types.md)
+ [Statements](ql-reference.statements.md)
+ [Functions](ql-functions.md)
+ [Operators](ql-operators.md)
+ [Transactions](ql-reference.multiplestatements.transactions.md)
+ [Batch operations](ql-reference.multiplestatements.batching.md)
+ [IAM policies](ql-iam.md)

## What is PartiQL?<a name="ql-reference.what-is"></a>

*PartiQL* provides SQL\-compatible query access across multiple data stores containing structured data, semistructured data, and nested data\. It is widely used within Amazon and is now available as part of many AWS services, including DynamoDB\.

For the PartiQL specification and a tutorial on the core query language, see the [PartiQL documentation](https://partiql.org/docs.html)\.

**Note**  
Amazon DynamoDB supports a *subset* of the [PartiQL](https://partiql.org/) query language\.
Amazon DynamoDB does not support the [Amazon ion](http://amzn.github.io/ion-docs/) data format or Amazon Ion literals\.

## PartiQL in Amazon DynamoDB<a name="ql-reference.what-is"></a>

To run PartiQL queries in DynamoDB, you can use:
+ The DynamoDB console
+ The NoSQL Workbench
+ The AWS Command Line Interface \(AWS CLI\)
+ The DynamoDB APIs

For information about using these methods to access DynamoDB, see [Accessing DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/AccessingDynamoDB.html)\.