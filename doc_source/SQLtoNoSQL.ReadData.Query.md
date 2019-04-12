# Querying a Table<a name="SQLtoNoSQL.ReadData.Query"></a>

Another common access pattern is reading multiple items from a table, based on your query criteria\.

## SQL<a name="SQLtoNoSQL.ReadData.Query.SQL"></a>

The SQL `SELECT` statement lets you query on key columns, non\-key columns, or any combination\. The `WHERE` clause determines which rows are returned, as shown in the following examples.

```
/* Return a single song, by primary key */

SELECT * FROM Music
WHERE Artist='No One You Know' AND SongTitle = 'Call Me Today';
```

```
/* Return all of the songs by an artist */

SELECT * FROM Music
WHERE Artist='No One You Know';
```

```
/* Return all of the songs by an artist, matching first part of title */

SELECT * FROM Music
WHERE Artist='No One You Know' AND SongTitle LIKE 'Call%';
```

```
/* Return all of the songs by an artist, with a particular word in the title... 
...but only if the price is less than 1.00 */

SELECT * FROM Music
WHERE Artist='No One You Know' AND SongTitle LIKE '%Today%'
AND Price < 1.00;
```

Note that the primary key for this table consists of *Artist* and *SongTitle*\.

## DynamoDB<a name="SQLtoNoSQL.ReadData.Query.DynamoDB"></a>

The Amazon DynamoDB `Query` action lets you retrieve data in a similar fashion\. The `Query` action provides quick, efficient access to the physical locations where the data is stored\. \(For more information, see [Partitions and Data Distribution](HowItWorks.Partitions.md)\.\)

You can use `Query` with any table that has a composite primary key \(partition key and sort key\)\. You must specify an equality condition for the partition key, and you can optionally provide another condition for the sort key\.

The `KeyConditionExpression` parameter specifies the key values that you want to query\. You can use an optional `FilterExpression` to remove certain items from the results before they are returned to you\.

In DynamoDB, you must use `ExpressionAttributeValues` as placeholders in expression parameters \(such as `KeyConditionExpression` and `FilterExpression`\)\. This is analogous to the use of *bind variables* in relational databases, where you substitute the actual values into the `SELECT` statement at runtime\.

Note that the primary key for this table consists of *Artist* and *SongTitle*\.

The following are DynamoDB `Query` examples.

```
// Return a single song, by primary key

{
    TableName: "Music",
    KeyConditionExpression: "Artist = :a and SongTitle = :t",
    ExpressionAttributeValues: {
        ":a": "No One You Know",
        ":t": "Call Me Today"
    }
}
```

```
// Return all of the songs by an artist

{
    TableName: "Music",
    KeyConditionExpression: "Artist = :a",
    ExpressionAttributeValues: {
        ":a": "No One You Know"
    }
}
```

```
// Return all of the songs by an artist, matching first part of title

{
    TableName: "Music",
    KeyConditionExpression: "Artist = :a and begins_with(SongTitle, :t)",
    ExpressionAttributeValues: {
        ":a": "No One You Know",
        ":t": "Call"
    }
}
```

```
// Return all of the songs by an artist, with a particular word in the title...
// ...but only if the price is less than 1.00

{
    TableName: "Music",
    KeyConditionExpression: "Artist = :a and contains(SongTitle, :t)",
    FilterExpression: "price < :p",
    ExpressionAttributeValues: {
        ":a": "No One You Know",
        ":t": "Today",
        ":p": 1.00
    }
}
```

**Note**  
For code samples using `Query`, see [Getting Started with DynamoDB](GettingStarted.md)\.
