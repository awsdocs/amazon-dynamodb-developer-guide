# From SQL to NoSQL<a name="SQLtoNoSQL"></a>

If you are an application developer, you might have some experience using a relational database management system \(RDBMS\) and Structured Query Language \(SQL\)\. As you begin working with Amazon DynamoDB, you will encounter many similarities, but also many things that are different\. *NoSQL* is a term used to describe nonrelational database systems that are highly available, scalable, and optimized for high performance\. Instead of the relational model, NoSQL databases \(like DynamoDB\) use alternate models for data management, such as key\-value pairs or document storage\. For more information, see [What is NoSQL?](http://aws.amazon.com/nosql)\.

Amazon DynamoDB supports [PartiQL](https://partiql.org/), an open\-source, SQL\-compatible query language that makes it easy for you to efficiently query data, regardless of where or in what format it is stored\. With PartiQL, you can easily process structured data from relational databases, semi\-structured and nested data in open data formats, and even schema\-less data in NoSQL or document databases that allow different attributes for different rows\. For more information, see [PartiQL query language](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ql-reference.html)\.

The following sections describe common database tasks, comparing and contrasting SQL statements with their equivalent DynamoDB operations\.

**Note**  
The SQL examples in this section are compatible with the MySQL RDBMS\.  
The DynamoDB examples in this section show the name of the DynamoDB operation, along with the parameters for that operation in JSON format\. For code examples that use these operations, see [Getting started with DynamoDB and the AWS SDKs](GettingStarted.md)\.

**Topics**
+ [Relational \(SQL\) or NoSQL?](SQLtoNoSQL.WhyDynamoDB.md)
+ [Characteristics of databases](SQLtoNoSQL.Accessing.md)
+ [Creating a table](SQLtoNoSQL.CreateTable.md)
+ [Getting information about a table](SQLtoNoSQL.GetTableInfo.md)
+ [Writing data to a table](SQLtoNoSQL.WriteData.md)
+ [Key differences when reading data from a table](SQLtoNoSQL.ReadData.md)
+ [Managing indexes](SQLtoNoSQL.Indexes.md)
+ [Modifying data in a table](SQLtoNoSQL.UpdateData.md)
+ [Deleting data from a table](SQLtoNoSQL.DeleteData.md)
+ [Removing a table](SQLtoNoSQL.RemoveTable.md)