# Modifying Data in a Table<a name="SQLtoNoSQL.UpdateData"></a>

The SQL language provides the `UPDATE` statement for modifying data\. DynamoDB uses the `UpdateItem` operation to accomplish similar tasks\.

## SQL<a name="SQLtoNoSQL.UpdateData.SQL"></a>

In SQL, you use the `UPDATE` statement to modify one or more rows\. The `SET` clause specifies new values for one or more columns, and the `WHERE` clause determines which rows are modified\. The following is an example.

```
UPDATE Music
SET RecordLabel = 'Global Records'
WHERE Artist = 'No One You Know' AND SongTitle = 'Call Me Today';
```

If no rows match the `WHERE` clause, the `UPDATE` statement has no effect\.

## DynamoDB<a name="SQLtoNoSQL.UpdateData.DynamoDB"></a>

In DynamoDB, you use the `UpdateItem` action to modify a single item\. \(If you want to modify multiple items, you must use multiple `UpdateItem` operations\.\)

The following is an example.

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

You must specify the `Key` attributes of the item to be modified and an `UpdateExpression` to specify attribute values\.

`UpdateItem` replaces the entire item, rather than replacing individual attributes\. `UpdateItem` behaves like an "upsert" operation: The item is updated if it exists in the table, but if not, a new item is added \(inserted\)\.

`UpdateItem` supports *conditional writes*, where the operation succeeds only if a specific `ConditionExpression` evaluates to true\. For example, the following `UpdateItem` action does not perform the update unless the price of the song is greater than or equal to 2\.00.

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

The following is an example of an `UpdateItem` action to initialize a new attribute \(*Plays*\) to keep track of the number of times a song has been played.

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

Whenever someone plays this song, we can use the following `UpdateItem` action to increment *Plays* by one.

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
For code samples using `UpdateItem`, see [Getting Started with DynamoDB](GettingStarted.md)\.
