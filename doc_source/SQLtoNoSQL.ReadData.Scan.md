# Scanning a table<a name="SQLtoNoSQL.ReadData.Scan"></a>

In SQL, a `SELECT` statement without a `WHERE` clause will return every row in a table\. In Amazon DynamoDB, the `Scan` operation does the same thing\. In both cases, you can retrieve all of the items or just some of them\.

Whether you are using a SQL or a NoSQL database, scans should be used sparingly because they can consume large amounts of system resources\. Sometimes a scan is appropriate \(such as scanning a small table\) or unavoidable \(such as performing a bulk export of data\)\. However, as a general rule, you should design your applications to avoid performing scans\.

**Note**  
Doing a bulk export also creates at least 1 file per partition\. All of the items in each file are from that particular partition's hashed keyspace\.

**Topics**
+ [SQL](#SQLtoNoSQL.ReadData.Scan.SQL)
+ [DynamoDB](#SQLtoNoSQL.ReadData.Scan.DynamoDB)

## SQL<a name="SQLtoNoSQL.ReadData.Scan.SQL"></a>

In SQL, you can scan a table and retrieve all of its data by using a `SELECT` statement without specifying a `WHERE` clause\. You can request one or more columns in the result\. Or you can request all of them if you use the wildcard character \(\*\)\.

The following are examples of using a `SELECT` statement\.

```
/* Return all of the data in the table */
SELECT * FROM Music;
```

```
/* Return all of the values for Artist and Title */
SELECT Artist, Title FROM Music;
```

## DynamoDB<a name="SQLtoNoSQL.ReadData.Scan.DynamoDB"></a>

In Amazon DynamoDB, you can use either the DynamoDB API, or [PartiQL](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ql-reference.html), a SQL\-compatible query language, to perform a scan on a table\.

------
#### [ DynamoDB API ]

With the DynamoDB API, you use the `Scan` operation to return one or more items and item attributes by accessing every item in a table or a secondary index\.

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

The `Scan` action also provides a `FilterExpression` parameter, which you can use to discard items that you do not want to appear in the results\. A `FilterExpression` is applied after the scan is performed, but before the results are returned to you\. \(This is not recommended with large tables\. You are still charged for the entire `Scan`, even if only a few matching items are returned\.\)

**Note**  
For code examples that use `Scan`, see [Getting started with DynamoDB and the AWS SDKs](GettingStarted.md)\.

------
#### [ PartiQL for DynamoDB ]

With PartiQL, you perform a scan by using the `ExecuteStatement` operation to return all the contents for a table using the `Select` statement\.

```
SELECT AlbumTitle, Year, Price
FROM Music
```

Note that this statement will return all items for in the Music table\. 

For code examples using `Select` and `ExecuteStatement`, see [PartiQL select statements for DynamoDB](ql-reference.select.md)\.

------