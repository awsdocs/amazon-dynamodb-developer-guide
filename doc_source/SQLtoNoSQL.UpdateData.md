# Modifying Data in a Table<a name="SQLtoNoSQL.UpdateData"></a>

The SQL language provides the `UPDATE` statement for modifying data\. Amazon DynamoDB uses the `UpdateItem` operation to accomplish similar tasks\.

**Topics**
+ [SQL](#SQLtoNoSQL.UpdateData.SQL)
+ [DynamoDB](#SQLtoNoSQL.UpdateData.DynamoDB)

## SQL<a name="SQLtoNoSQL.UpdateData.SQL"></a>

In SQL, you use the `UPDATE` statement to modify one or more rows\. The `SET` clause specifies new values for one or more columns, and the `WHERE` clause determines which rows are modified\. The following is an example\.

```
UPDATE Music
SET RecordLabel = 'Global Records'
WHERE Artist = 'No One You Know' AND SongTitle = 'Call Me Today';
```

If no rows match the `WHERE` clause, the `UPDATE` statement has no effect\.

## DynamoDB<a name="SQLtoNoSQL.UpdateData.DynamoDB"></a>

In DynamoDB, you can use either the classic API, or [PartiQL](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ql-reference.html), a SQL\-compatible query language to modify a single item\. \(If you want to modify multiple items, you must use multiple operations\.\)

------
#### [ DynamoDB API ]

With the DynamoDB API, you use the `UpdateItem` action to modify a single item\.

```
{
    TableName: "Music",
    Key: {
        "Artist":"No One You Know",
        "SongTitle":"Call Me Today"
    },
    UpdateExpression: "SET RecordLabel = :label",
    ExpressionAttributeValues: {
        ":label": "Global Records"
    }
}
```

You must specify the `Key` attributes of the item to be modified and an `UpdateExpression` to specify attribute values\. `UpdateItem` behaves like an "upsert" operation: The item is updated if it exists in the table, but if not, a new item is added \(inserted\)\.

`UpdateItem` supports *conditional writes*, where the operation succeeds only if a specific `ConditionExpression` evaluates to true\. For example, the following `UpdateItem` action does not perform the update unless the price of the song is greater than or equal to 2\.00\.

```
{
    TableName: "Music",
    Key: {
        "Artist":"No One You Know",
        "SongTitle":"Call Me Today"
    },
    UpdateExpression: "SET RecordLabel = :label",
    ConditionExpression: "Price >= :p",
    ExpressionAttributeValues: {
        ":label": "Global Records",
        ":p": 2.00
    }
}
```

`UpdateItem` also supports *atomic counters*, or attributes of type `Number` that can be incremented or decremented\. Atomic counters are similar in many ways to sequence generators, identity columns, or autoincrement fields in SQL databases\. 

The following is an example of an `UpdateItem` action to initialize a new attribute \(*Plays*\) to keep track of the number of times a song has been played\.

```
{
    TableName: "Music",
    Key: {
        "Artist":"No One You Know",
        "SongTitle":"Call Me Today"
    },
    UpdateExpression: "SET Plays = :val",
    ExpressionAttributeValues: {
        ":val": 0
    },
    ReturnValues: "UPDATED_NEW"
}
```

The `ReturnValues` parameter is set to `UPDATED_NEW`, which returns the new values of any attributes that were updated\. In this case, it returns 0 \(zero\)\.

Whenever someone plays this song, we can use the following `UpdateItem` action to increment *Plays* by one\.

```
{
    TableName: "Music",
    Key: {
        "Artist":"No One You Know",
        "SongTitle":"Call Me Today"
    },
    UpdateExpression: "SET Plays = Plays + :incr",
    ExpressionAttributeValues: {
        ":incr": 1
    },
    ReturnValues: "UPDATED_NEW"
}
```

**Note**  
For code examples that use `UpdateItem`, see [Getting Started with DynamoDB and AWS SDKs](GettingStarted.md)\.

------
#### [ PartiQL for DynamoDB ]

With PartiQL, you use the `ExecuteStatement` operation to modify an item in a table, using the PartiQL `Update` statement\.

The primary key for this table consists of *Artist* and *SongTitle*\. You must specify values for these attributes\.

```
UPDATE Music
SET RecordLabel ='Global Records'
WHERE Artist='No One You Know' AND SongTitle='Call Me Today'
```

You can also modify multiple fields at once, such as in the following example\.

```
UPDATE Music
SET RecordLabel = 'Global Records'
SET AwardsWon = 10
WHERE Artist ='No One You Know' AND SongTitle='Call Me Today'
```

`Update` also supports *atomic counters*, or attributes of type `Number` that can be incremented or decremented\. Atomic counters are similar in many ways to sequence generators, identity columns, or autoincrement fields in SQL databases\.

The following is an example of an `Update` statement to initialize a new attribute \(*Plays*\) to keep track of the number of times a song has been played\.

```
UPDATE Music
SET Plays = 0
WHERE Artist='No One You Know' AND SongTitle='Call Me Today'
```

Whenever someone plays this song, we can use the following `Update` statement to increment *Plays* by one\.

```
UPDATE Music
SET Plays = Plays + 1
WHERE Artist='No One You Know' AND SongTitle='Call Me Today'
```

**Note**  
For code examples using `Update` and `ExecuteStatement`, see [PartiQL Update Statements for DynamoDB](ql-reference.update.md)\.

------