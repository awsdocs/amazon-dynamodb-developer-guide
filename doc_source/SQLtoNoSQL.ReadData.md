# Key Differences When Reading Data from a Table<a name="SQLtoNoSQL.ReadData"></a>

With SQL, you use the `SELECT` statement to retrieve one or more rows from a table\. You use the `WHERE` clause to determine the data that is returned to you\.

Amazon DynamoDB provides the following operations for reading data:
+ `ExecuteStatement` retrieves a single or multiple items from a table\. `BatchExecuteStatement` retrieves multiple items from different tables in a single operation\. Both of these operations use [PartiQL](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ql-reference.html), a SQL\-compatible query language\.
+ `GetItem` – Retrieves a single item from a table\. This is the most efficient way to read a single item because it provides direct access to the physical location of the item\. \(DynamoDB also provides the `BatchGetItem` operation, allowing you to perform up to 100 `GetItem` calls in a single operation\.\)
+ `Query` – Retrieves all of the items that have a specific partition key\. Within those items, you can apply a condition to the sort key and retrieve only a subset of the data\. `Query` provides quick, efficient access to the partitions where the data is stored\. \(For more information, see [Partitions and Data Distribution](HowItWorks.Partitions.md)\.\)
+ `Scan` – Retrieves all of the items in the specified table\. \(This operation should not be used with large tables because it can consume large amounts of system resources\.\)

**Note**  
With a relational database, you can use the `SELECT` statement to join data from multiple tables and return the results\. Joins are fundamental to the relational model\. To ensure that joins run efficiently, the database and its applications should be performance\-tuned on an ongoing basis\. DynamoDB is a non\-relational NoSQL database that does not support table joins\. Instead, applications read data from one table at a time\. 

The following sections describe different use cases for reading data, and how to perform these tasks with a relational database and with DynamoDB\.

**Topics**
+ [Reading an Item Using Its Primary Key](SQLtoNoSQL.ReadData.SingleItem.md)
+ [Querying a Table](SQLtoNoSQL.ReadData.Query.md)
+ [Scanning a Table](SQLtoNoSQL.ReadData.Scan.md)