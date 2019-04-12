# Reading an Item Using Its Primary Key<a name="SQLtoNoSQL.ReadData.SingleItem"></a>

One common access pattern for databases is to read a single item from a table\. You have to specify the primary key of the item you want\.

## SQL<a name="SQLtoNoSQL.ReadData.SingleItem.SQL"></a>

In SQL, you use the `SELECT` statement to retrieve data from a table\. You can request one or more columns in the result \(or all of them, if you use the `*` operator\)\. The `WHERE` clause determines which rows to return\.

The following is a `SELECT` statement to retrieve a single row from the *Music* table\. The `WHERE` clause specifies the primary key values\.

```
SELECT * 
FROM Music 
WHERE Artist='No One You Know' AND SongTitle = 'Call Me Today'
```

You can modify the following query to retrieve only a subset of the columns.

```
SELECT AlbumTitle, Year, Price
FROM Music 
WHERE Artist='No One You Know' AND SongTitle = 'Call Me Today'
```

Note that the primary key for this table consists of *Artist* and *SongTitle*\.

## DynamoDB<a name="SQLtoNoSQL.ReadData.SingleItem.DynamoDB"></a>

DynamoDB provides the `GetItem` action for retrieving an item by its primary key\. `GetItem` is highly efficient because it provides direct access to the physical location of the item\. \(For more information, see [Partitions and Data Distribution](HowItWorks.Partitions.md)\.\)

By default, `GetItem` returns the entire item with all of its attributes\.

```
{
    TableName: "Music",
    Key: {
        "Artist": "No One You Know",
        "SongTitle": "Call Me Today"
    }
}
```

You can add a `ProjectionExpression` parameter to return only some of the attributes.

```
{
    TableName: "Music",
    Key: {
        "Artist": "No One You Know",
        "SongTitle": "Call Me Today"
    },
    "ProjectionExpression": "AlbumTitle, Year, Price"
}
```

Note that the primary key for this table consists of *Artist* and *SongTitle*\.

The DynamoDB `GetItem` action is very efficient: It uses the primary key values to determine the exact storage location of the item in question, and retrieves it directly from there\. The SQL `SELECT` statement is similarly efficient, in the case of retrieving items by primary key values\.

The SQL `SELECT` statement supports many kinds of queries and table scans\. DynamoDB provides similar functionality with its `Query` and `Scan` actions, which are described in [Querying a Table](SQLtoNoSQL.ReadData.Query.md) and [Scanning a Table](SQLtoNoSQL.ReadData.Scan.md)\.

The SQL `SELECT` statement can perform table joins, allowing you to retrieve data from multiple tables at the same time\. Joins are most effective where the database tables are normalized and the relationships among the tables are clear\. However, if you join too many tables in one `SELECT` statement application performance can be affected\. You can work around such issues by using database replication, materialized views, or query rewrites\.

DynamoDB is a nonrelational database\. As such, it does not support table joins\. If you are migrating an existing application from a relational database to DynamoDB, you need to denormalize your data model to eliminate the need for joins\.

**Note**  
For code samples using `GetItem`, see [Getting Started with DynamoDB](GettingStarted.md)\.
