# Scanning a Table<a name="SQLtoNoSQL.ReadData.Scan"></a>

In SQL, a `SELECT` statement without a `WHERE` clause will return every row in a table\. In DynamoDB, the `Scan` operation does the same thing\. In both cases, you can retrieve all of the items or just some of them\.

Whether you are using a SQL or a NoSQL database, scans should be used sparingly because they can consume large amounts of system resources\. Sometimes a scan is appropriate \(such as scanning a small table\) or unavoidable \(such as performing a bulk export of data\)\. However, as a general rule, you should design your applications to avoid performing scans\.

## SQL<a name="SQLtoNoSQL.ReadData.Scan.SQL"></a>

In SQL, you can scan a table and retrieve all of its data by using a `SELECT` statement without specifying a `WHERE` clause\. You can request one or more columns in the result\. Or you can request all of them if you use the wildcard character \(\*\)\.

The following are two examples of using a `SELECT` statement.

```
/* Return all of the data in the table */
SELECT * FROM Music;
```

```
/* Return all of the values for Artist and Title */
SELECT Artist, Title FROM Music;
```

## DynamoDB<a name="SQLtoNoSQL.ReadData.Scan.DynamoDB"></a>

DynamoDB provides a `Scan` action that works in a similar way\. The following are two examples.

```
// Return all of the data in the table
{
    TableName:  "Music"
}
```

```
// Return all of the values for Artist and Title
{
    TableName:  "Music",
    ProjectionExpression: "Artist, Title"
}
```

The `Scan` action also provides a `FilterExpression` parameter, which you can use to discard items that you do not want to appear in the results\. A `FilterExpression` is applied after the entire table is scanned, but before the results are returned to you\. \(This is not recommended with large tables: You are still charged for the entire `Scan`, even if only a few matching items are returned\.\)

**Note**  
For code samples that use `Scan`, see [Getting Started with DynamoDB](GettingStarted.md)\.
