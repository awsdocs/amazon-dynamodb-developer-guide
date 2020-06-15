# From SQL to NoSQL<a name="SQLtoNoSQL"></a>

If you are an application developer, you might have some experience using a relational database management system \(RDBMS\) and Structured Query Language \(SQL\)\. As you begin working with Amazon DynamoDB, you will encounter many similarities, but also many things that are different\. This section describes common database tasks, comparing and contrasting SQL statements with their equivalent DynamoDB operations\.

*NoSQL* is a term used to describe nonrelational database systems that are highly available, scalable, and optimized for high performance\. Instead of the relational model, NoSQL databases \(like DynamoDB\) use alternate models for data management, such as key\-value pairs or document storage\. For more information, see [http://aws\.amazon\.com/nosql](http://aws.amazon.com/nosql)\.

**Note**  
The SQL examples in this section are compatible with the MySQL RDBMS\.  
The DynamoDB examples in this section show the name of the DynamoDB operation, along with the parameters for that operation in JSON format\. For code examples that use these operations, see [Getting Started with DynamoDB and AWS SDKs](GettingStarted.md)\.

**Topics**
+ [Relational \(SQL\) or NoSQL?](SQLtoNoSQL.WhyDynamoDB.md)
+ [Characteristics of Databases](SQLtoNoSQL.Accessing.md)
+ [Creating a Table](SQLtoNoSQL.CreateTable.md)
+ [Getting Information About a Table](SQLtoNoSQL.GetTableInfo.md)
+ [Writing Data to a Table](SQLtoNoSQL.WriteData.md)
+ [Key Differences When Reading Data from a Table](SQLtoNoSQL.ReadData.md)
+ [Managing Indexes](SQLtoNoSQL.Indexes.md)
+ [Modifying Data in a Table](SQLtoNoSQL.UpdateData.md)
+ [Deleting Data from a Table](SQLtoNoSQL.DeleteData.md)
+ [Removing a Table](SQLtoNoSQL.RemoveTable.md)