# Deleting data from a table<a name="SQLtoNoSQL.DeleteData"></a>

In SQL, the `DELETE` statement removes one or more rows from a table\. Amazon DynamoDB uses the `DeleteItem` operation to delete one item at a time\.

**Topics**
+ [SQL](#SQLtoNoSQL.DeleteData.SQL)
+ [DynamoDB](#SQLtoNoSQL.DeleteData.DynamoDB)

## SQL<a name="SQLtoNoSQL.DeleteData.SQL"></a>

In SQL, you use the `DELETE` statement to delete one or more rows\. The `WHERE` clause determines the rows that you want to modify\. The following is an example\.

```
DELETE FROM Music
WHERE Artist = 'The Acme Band' AND SongTitle = 'Look Out, World';
```

You can modify the `WHERE` clause to delete multiple rows\. For example, you could delete all of the songs by a particular artist, as shown in the following example\.

```
DELETE FROM Music WHERE Artist = 'The Acme Band'
```

**Note**  
If you omit the `WHERE` clause, the database attempts to delete all of the rows from the table\.

## DynamoDB<a name="SQLtoNoSQL.DeleteData.DynamoDB"></a>

In DynamoDB, you can use either the classic API, or [PartiQL](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ql-reference.html), a SQL\-compatible query language to delete a single item\. \(If you want to modify multiple items, you must use multiple operations\.\)

------
#### [ DynamoDB API ]

With the DynamoDB API, you use the `DeleteItem` action to delete data from a table, one item at a time\. You must specify the item's primary key values\.

```
{
    TableName: "Music",
    Key: {
        Artist: "The Acme Band",
        SongTitle: "Look Out, World"
    }
}
```

**Note**  
In addition to `DeleteItem`, Amazon DynamoDB supports a `BatchWriteItem` action for deleting multiple items at the same time\.

`DeleteItem` supports *conditional writes*, where the operation succeeds only if a specific `ConditionExpression` evaluates to true\. For example, the following `DeleteItem` action deletes the item only if it has a *RecordLabel* attribute\.

```
{
    TableName: "Music",
    Key: {
        Artist: "The Acme Band",
        SongTitle: "Look Out, World"
    },
   ConditionExpression: "attribute_exists(RecordLabel)"
}
```

**Note**  
For code examples that use `DeleteItem`, see [Getting started with DynamoDB and the AWS SDKs](GettingStarted.md)\.

------
#### [ PartiQL for DynamoDB ]

With PartiQL, you use the `Delete` statement through the `ExecuteStatement` operation to delete data from a table, one item at a time\. You must specify the item's primary key values\.

The primary key for this table consists of *Artist* and *SongTitle*\. You must specify values for these attributes\.

```
DELETE FROM Music
WHERE Artist = 'Acme Band' AND SongTitle = 'PartiQL Rocks'
```

You can also specify additional conditions for the operation\. The following `DELETE` operation only deletes the item if it has more than 11 *Awards*\.

```
DELETE FROM Music
WHERE Artist = 'Acme Band' AND SongTitle = 'PartiQL Rocks' AND Awards > 11
```

**Note**  
For code examples using `DELETE` and `ExecuteStatement`, see [PartiQL delete statements for DynamoDB](ql-reference.delete.md)\.

------