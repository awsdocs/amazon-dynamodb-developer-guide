# Querying and Scanning an Index<a name="SQLtoNoSQL.Indexes.QueryAndScan"></a>

## SQL<a name="SQLtoNoSQL.Indexes.QueryAndScan.SQL"></a>

In a relational database, you do not work directly with indexes\. Instead, you query tables by issuing `SELECT` statements, and the query optimizer can make use of any indexes\.

A *query optimizer* is a relational database management system \(RDBMS\) component that evaluates the available indexes, and determines whether they can be used to speed up a query\. If the indexes can be used to speed up a query, the RDBMS accesses the index first and then uses it to locate the data in the table\.

Here are some SQL statements that can use *GenreAndPriceIndex* to improve performance\. We assume that the *Music* table has enough data in it that the query optimizer decides to use this index, rather than simply scanning the entire table\.

```
/* All of the rock songs */

SELECT * FROM Music 
WHERE Genre = 'Rock';
```

```
/* All of the cheap country songs */

SELECT Artist, SongTitle, Price FROM Music 
WHERE Genre = 'Country' AND Price < 0.50;
```

## DynamoDB<a name="SQLtoNoSQL.Indexes.QueryAndScan.DynamoDB"></a>

In DynamoDB, you perform `Query` operations directly on the index, in the same way that you would on a table\. You must specify both `TableName` and `IndexName`\.

The following are some queries on *GenreAndPriceIndex* in DynamoDB\. \(The key schema for this index consists of *Genre* and *Price*\.\)

```
// All of the rock songs

{
    TableName: "Music",
    IndexName: "GenreAndPriceIndex",
    KeyConditionExpression: "Genre = :genre",
    ExpressionAttributeValues: {
        ":genre": "Rock"
    },
};
```

```
// All of the cheap country songs

{
    TableName: "Music",
    IndexName: "GenreAndPriceIndex",
    KeyConditionExpression: "Genre = :genre and Price < :price",
    ExpressionAttributeValues: {
        ":genre": "Country",
        ":price": 0.50
    },
    ProjectionExpression: "Artist, SongTitle, Price"
};
```

This example uses a `ProjectionExpression` to indicate that we only want some of the attributes, rather than all of them, to appear in the results\.

You can also perform `Scan` operations on a secondary index, in the same way that you would on a table\. The following is a scan on *GenreAndPriceIndex*:

```
// Return all of the data in the index

{
    TableName:  "Music",
    IndexName: "GenreAndPriceIndex"
}
```
