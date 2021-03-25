# DynamoDB API<a name="HowItWorks.API"></a>

To work with Amazon DynamoDB, your application must use a few simple API operations\. The following is a summary of these operations, organized by category\.

**Topics**
+ [Control Plane](#HowItWorks.API.ControlPlane)
+ [Data Plane](#HowItWorks.API.DataPlane)
+ [DynamoDB Streams](#HowItWorks.API.Streams)
+ [Transactions](#HowItWorks.API.Transactions)

## Control Plane<a name="HowItWorks.API.ControlPlane"></a>

*Control plane* operations let you create and manage DynamoDB tables\. They also let you work with indexes, streams, and other objects that are dependent on tables\.
+  `CreateTable` – Creates a new table\. Optionally, you can create one or more secondary indexes, and enable DynamoDB Streams for the table\.
+ `DescribeTable`– Returns information about a table, such as its primary key schema, throughput settings, and index information\.
+ `ListTables` – Returns the names of all of your tables in a list\.
+ `UpdateTable` – Modifies the settings of a table or its indexes, creates or removes new indexes on a table, or modifies DynamoDB Streams settings for a table\.
+ `DeleteTable` – Removes a table and all of its dependent objects from DynamoDB\.

## Data Plane<a name="HowItWorks.API.DataPlane"></a>

*Data plane* operations let you perform create, read, update, and delete \(also called *CRUD*\) actions on data in a table\. Some of the data plane operations also let you read data from a secondary index\.

You can use [PartiQL \- A SQL\-Compatible Query Language for Amazon DynamoDB](ql-reference.md), to perform these CRUD operations or you can use DynamoDB’s classic CRUD APIs that separates each operation into a distinct API call\. 

### PartiQL \- A SQL\-Compatible Query Language<a name="HowItWorks.API.DataPlane.partiql"></a>
+ `ExecuteStatement` – Reads multiple items from a table\. You can also write or update a single item from a table\. When writing or updating a single item, you must specify the primary key attributes\.
+ `BatchExecuteStatement` – Writes, updates or reads multiple items from a table\. This is more efficient than `ExecuteStatement` because your application only needs a single network round trip to write or read the items\.

### Classic APIs<a name="HowItWorks.API.DataPlane.classic"></a>

#### Creating Data<a name="HowItWorks.API.DataPlane.Create"></a>
+ `PutItem` – Writes a single item to a table\. You must specify the primary key attributes, but you don't have to specify other attributes\.
+ `BatchWriteItem` – Writes up to 25 items to a table\. This is more efficient than calling `PutItem` multiple times because your application only needs a single network round trip to write the items\. You can also use `BatchWriteItem` for deleting multiple items from one or more tables\.

#### Reading Data<a name="HowItWorks.API.DataPlane.Read"></a>
+ `GetItem` – Retrieves a single item from a table\. You must specify the primary key for the item that you want\. You can retrieve the entire item, or just a subset of its attributes\.
+ `BatchGetItem` – Retrieves up to 100 items from one or more tables\. This is more efficient than calling `GetItem` multiple times because your application only needs a single network round trip to read the items\.
+ `Query` – Retrieves all items that have a specific partition key\. You must specify the partition key value\. You can retrieve entire items, or just a subset of their attributes\. Optionally, you can apply a condition to the sort key values so that you only retrieve a subset of the data that has the same partition key\. You can use this operation on a table, provided that the table has both a partition key and a sort key\. You can also use this operation on an index, provided that the index has both a partition key and a sort key\.
+ `Scan` – Retrieves all items in the specified table or index\. You can retrieve entire items, or just a subset of their attributes\. Optionally, you can apply a filtering condition to return only the values that you are interested in and discard the rest\.

#### Updating Data<a name="HowItWorks.API.DataPlane.Update"></a>
+ `UpdateItem` – Modifies one or more attributes in an item\. You must specify the primary key for the item that you want to modify\. You can add new attributes and modify or remove existing attributes\. You can also perform conditional updates, so that the update is only successful when a user\-defined condition is met\. Optionally, you can implement an atomic counter, which increments or decrements a numeric attribute without interfering with other write requests\.

#### Deleting Data<a name="HowItWorks.API.DataPlane.Delete"></a>
+ `DeleteItem` – Deletes a single item from a table\. You must specify the primary key for the item that you want to delete\.
+ `BatchWriteItem` – Deletes up to 25 items from one or more tables\. This is more efficient than calling `DeleteItem` multiple times because your application only needs a single network round trip to delete the items\. You can also use `BatchWriteItem` for adding multiple items to one or more tables\.

## DynamoDB Streams<a name="HowItWorks.API.Streams"></a>

*DynamoDB Streams* operations let you enable or disable a stream on a table, and allow access to the data modification records contained in a stream\.
+ `ListStreams` – Returns a list of all your streams, or just the stream for a specific table\.
+ `DescribeStream` – Returns information about a stream, such as its Amazon Resource Name \(ARN\) and where your application can begin reading the first few stream records\.
+ `GetShardIterator` – Returns a *shard iterator*, which is a data structure that your application uses to retrieve the records from the stream\.
+ `GetRecords` – Retrieves one or more stream records, using a given shard iterator\.

## Transactions<a name="HowItWorks.API.Transactions"></a>

*Transactions* provide atomicity, consistency, isolation, and durability \(ACID\) enabling you to maintain data correctness in your applications more easily\.

You can use [PartiQL \- A SQL\-Compatible Query Language for Amazon DynamoDB](ql-reference.md), to perform transactional operations or you can use DynamoDB’s classic CRUD APIs that separates each operation into a distinct API call\.

### PartiQL \- A SQL\-Compatible Query Language<a name="HowItWorks.API.DataPlane.partiql"></a>
+ `ExecuteTransaction` – A batch operation that allows CRUD operations to multiple items both within and across tables with a guaranteed all\-or\-nothing result\.

### Classic APIs<a name="HowItWorks.API.DataPlane.classic"></a>
+ `TransactWriteItems` – A batch operation that allows `Put`, `Update`, and `Delete` operations to multiple items both within and across tables with a guaranteed all\-or\-nothing result\.
+ `TransactGetItems` – A batch operation that allows `Get` operations to retrieve multiple items from one or more tables\.