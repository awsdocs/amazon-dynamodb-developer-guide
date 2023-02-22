# Creating an index<a name="SQLtoNoSQL.Indexes.Creating"></a>

Compare the `CREATE INDEX` statement in SQL with the `UpdateTable` operation in Amazon DynamoDB\.

**Topics**
+ [SQL](#SQLtoNoSQL.Indexes.Creating.SQL)
+ [DynamoDB](#SQLtoNoSQL.Indexes.Creating.DynamoDB)

## SQL<a name="SQLtoNoSQL.Indexes.Creating.SQL"></a>

In a relational database, an index is a data structure that lets you perform fast queries on different columns in a table\. You can use the `CREATE INDEX` SQL statement to add an index to an existing table, specifying the columns to be indexed\. After the index has been created, you can query the data in the table as usual, but now the database can use the index to quickly find the specified rows in the table instead of scanning the entire table\.

After you create an index, the database maintains it for you\. Whenever you modify data in the table, the index is automatically modified to reflect changes in the table\.

In MySQL, you can create an index like the following\.

```
CREATE INDEX GenreAndPriceIndex
ON Music (genre, price);
```

## DynamoDB<a name="SQLtoNoSQL.Indexes.Creating.DynamoDB"></a>

In DynamoDB, you can create and use a *secondary index* for similar purposes\.

Indexes in DynamoDB are different from their relational counterparts\. When you create a secondary index, you must specify its key attributes—a partition key and a sort key\. After you create the secondary index, you can `Query` it or `Scan` it just as you would with a table\. DynamoDB does not have a query optimizer, so a secondary index is only used when you `Query` it or `Scan` it\.

DynamoDB supports two different kinds of indexes:
+ Global secondary indexes – The primary key of the index can be any two attributes from its table\. 
+ Local secondary indexes – The partition key of the index must be the same as the partition key of its table\. However, the sort key can be any other attribute\.

DynamoDB ensures that the data in a secondary index is eventually consistent with its table\. You can request strongly consistent `Query` or `Scan` actions on a table or a local secondary index\. However, global secondary indexes support only eventual consistency\.

You can add a global secondary index to an existing table, using the `UpdateTable` action and specifying `GlobalSecondaryIndexUpdates`\.

```
{
    TableName: "Music",
    AttributeDefinitions:[
        {AttributeName: "Genre", AttributeType: "S"},
        {AttributeName: "Price", AttributeType: "N"}
    ],
    GlobalSecondaryIndexUpdates: [
        {
            Create: {
                IndexName: "GenreAndPriceIndex",
                KeySchema: [
                    {AttributeName: "Genre", KeyType: "HASH"}, //Partition key
                    {AttributeName: "Price", KeyType: "RANGE"}, //Sort key
                ],
                Projection: {
                    "ProjectionType": "ALL"
                },
                ProvisionedThroughput: {                                // Only specified if using provisioned mode
                    "ReadCapacityUnits": 1,"WriteCapacityUnits": 1
                }
            }
        }
    ]
}
```

You must provide the following parameters to `UpdateTable`:
+ `TableName` – The table that the index will be associated with\.
+ `AttributeDefinitions` – The data types for the key schema attributes of the index\.
+ `GlobalSecondaryIndexUpdates` – Details about the index you want to create:
  + `IndexName` – A name for the index\.
  + `KeySchema` – The attributes that are used for the index's primary key\.
  + `Projection` – Attributes from the table that are copied to the index\. In this case, `ALL` means that all of the attributes are copied\.
  + `ProvisionedThroughput (for provisioned tables)` – The number of reads and writes per second that you need for this index\. \(This is separate from the provisioned throughput settings of the table\.\) 

Part of this operation involves backfilling data from the table into the new index\. During backfilling, the table remains available\. However, the index is not ready until its `Backfilling` attribute changes from true to false\. You can use the `DescribeTable` action to view this attribute\.

**Note**  
For code examples that use `UpdateTable`, see [Getting started with DynamoDB and the AWS SDKs](GettingStarted.md)\.