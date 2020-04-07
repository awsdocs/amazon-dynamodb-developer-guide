# Removing a Table<a name="SQLtoNoSQL.RemoveTable"></a>

In SQL, you use the `DROP TABLE` statement to remove a table\. In Amazon DynamoDB, you use the `DeleteTable` operation\.

**Topics**
+ [SQL](#SQLtoNoSQL.RemoveTable.SQL)
+ [DynamoDB](#SQLtoNoSQL.RemoveTable.DynamoDB)

## SQL<a name="SQLtoNoSQL.RemoveTable.SQL"></a>

When you no longer need a table and want to discard it permanently, you use the `DROP TABLE` statement in SQL\.

```
DROP TABLE Music;
```

After a table is dropped, it cannot be recovered\. \(Some relational databases do allow you to undo a `DROP TABLE` operation, but this is vendor\-specific functionality and it is not widely implemented\.\)

## DynamoDB<a name="SQLtoNoSQL.RemoveTable.DynamoDB"></a>

DynamoDB has a similar action: `DeleteTable`\. In the following example, the table is permanently deleted\.

```
{
    TableName: "Music"
}
```

**Note**  
For code examples that use `DeleteTable`, see [Getting Started with DynamoDB and AWS SDKs](GettingStarted.md)\.