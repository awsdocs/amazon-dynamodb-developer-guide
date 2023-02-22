# Getting information about a table<a name="SQLtoNoSQL.GetTableInfo"></a>

You can verify that a table has been created according to your specifications\. In a relational database, all of the table's schema is shown\. Amazon DynamoDB tables are schemaless, so only the primary key attributes are shown\.

**Topics**
+ [SQL](#SQLtoNoSQL.GetTableInfo.SQL)
+ [DynamoDB](#SQLtoNoSQL.GetTableInfo.DynamoDB)

## SQL<a name="SQLtoNoSQL.GetTableInfo.SQL"></a>

Most relational database management systems \(RDBMS\) allow you to describe a table's structure—columns, data types, primary key definition, and so on\. There is no standard way to do this in SQL\. However, many database systems provide a `DESCRIBE` command\. The following is an example from MySQL\.

```
DESCRIBE Music;
```

This returns the structure of your table, with all of the column names, data types, and sizes\.

```
+------------+-------------+------+-----+---------+-------+
| Field      | Type        | Null | Key | Default | Extra |
+------------+-------------+------+-----+---------+-------+
| Artist     | varchar(20) | NO   | PRI | NULL    |       |
| SongTitle  | varchar(30) | NO   | PRI | NULL    |       |
| AlbumTitle | varchar(25) | YES  |     | NULL    |       |
| Year       | int(11)     | YES  |     | NULL    |       |
| Price      | float       | YES  |     | NULL    |       |
| Genre      | varchar(10) | YES  |     | NULL    |       |
| Tags       | text        | YES  |     | NULL    |       |
+------------+-------------+------+-----+---------+-------+
```

The primary key for this table consists of *Artist* and *SongTitle*\.

## DynamoDB<a name="SQLtoNoSQL.GetTableInfo.DynamoDB"></a>

DynamoDB has a `DescribeTable` action, which is similar\. The only parameter is the table name\.

```
{
    TableName : "Music"
}
```

The reply from `DescribeTable` looks like the following\.

```
{
  "Table": {
    "AttributeDefinitions": [
      {
        "AttributeName": "Artist",
        "AttributeType": "S"
      },
      {
        "AttributeName": "SongTitle",
        "AttributeType": "S"
      }
    ],
    "TableName": "Music",
    "KeySchema": [
      {
        "AttributeName": "Artist",
        "KeyType": "HASH"  //Partition key
      },
      {
        "AttributeName": "SongTitle",
        "KeyType": "RANGE"  //Sort key
      }
    ],

    ...
```

`DescribeTable` also returns information about indexes on the table, provisioned throughput settings, an approximate item count, and other metadata\.