# Creating a table<a name="SQLtoNoSQL.CreateTable"></a>

Tables are the fundamental data structures in relational databases and in Amazon DynamoDB\. A relational database management system \(RDBMS\) requires you to define the table's schema when you create it\. In contrast, DynamoDB tables are schemaless—other than the primary key, you do not need to define any extra attributes or data types when you create a table\.

**Topics**
+ [SQL](#SQLtoNoSQL.CreateTable.SQL)
+ [DynamoDB](#SQLtoNoSQL.CreateTable.DynamoDB)

## SQL<a name="SQLtoNoSQL.CreateTable.SQL"></a>

Use the `CREATE TABLE` statement to create a table, as shown in the following example\.

```
CREATE TABLE Music (
    Artist VARCHAR(20) NOT NULL,
    SongTitle VARCHAR(30) NOT NULL,
    AlbumTitle VARCHAR(25),
    Year INT,
    Price FLOAT,
    Genre VARCHAR(10),
    Tags TEXT,
    PRIMARY KEY(Artist, SongTitle)
);
```

The primary key for this table consists of *Artist* and *SongTitle*\.

You must define all of the table's columns and data types, and the table's primary key\. \(You can use the `ALTER TABLE` statement to change these definitions later, if necessary\.\)

Many SQL implementations let you define storage specifications for your table, as part of the `CREATE TABLE` statement\. Unless you indicate otherwise, the table is created with default storage settings\. In a production environment, a database administrator can help determine the optimal storage parameters\.

## DynamoDB<a name="SQLtoNoSQL.CreateTable.DynamoDB"></a>

Use the `CreateTable` action to create a provisioned mode table, specifying parameters as shown following:

```
{
    TableName : "Music",
    KeySchema: [
        {
            AttributeName: "Artist",
            KeyType: "HASH", //Partition key
        },
        {
            AttributeName: "SongTitle",
            KeyType: "RANGE" //Sort key
        }
    ],
    AttributeDefinitions: [
        {
            AttributeName: "Artist",
            AttributeType: "S"
        },
        {
            AttributeName: "SongTitle",
            AttributeType: "S"
        }
    ],
    ProvisionedThroughput: {       // Only specified if using provisioned mode
        ReadCapacityUnits: 1,
        WriteCapacityUnits: 1
    }
}
```

The primary key for this table consists of *Artist* \(partition key\) and *SongTitle* \(sort key\)\.

You must provide the following parameters to `CreateTable`:
+ `TableName` – Name of the table\.
+ `KeySchema` – Attributes that are used for the primary key\. For more information, see [Tables, items, and attributes](HowItWorks.CoreComponents.md#HowItWorks.CoreComponents.TablesItemsAttributes) and [Primary key](HowItWorks.CoreComponents.md#HowItWorks.CoreComponents.PrimaryKey)\.
+ `AttributeDefinitions` – Data types for the key schema attributes\.
+ `ProvisionedThroughput (for provisioned tables)` – Number of reads and writes per second that you need for this table\. DynamoDB reserves sufficient storage and system resources so that your throughput requirements are always met\. You can use the `UpdateTable` action to change these later, if necessary\. You do not need to specify a table's storage requirements because storage allocation is managed entirely by DynamoDB\.

**Note**  
For code examples that use `CreateTable`, see [Getting started with DynamoDB and the AWS SDKs](GettingStarted.md)\.