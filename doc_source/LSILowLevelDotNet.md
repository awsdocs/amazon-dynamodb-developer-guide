# Working with Local Secondary Indexes: \.NET<a name="LSILowLevelDotNet"></a>

**Topics**
+ [Create a table with a Local Secondary Index](#LSILowLevelDotNet.CreateTableWithIndex)
+ [Describe a table with a Local Secondary Index](#LSILowLevelDotNet.DescribeTableWithIndex)
+ [Query a Local Secondary Index](#LSILowLevelDotNet.QueryAnIndex)
+ [Example: Local Secondary Indexes using the AWS SDK for \.NET low\-level API](LSILowLevelDotNet.Example.md)

You can use the AWS SDK for \.NET low\-level API to create an Amazon DynamoDB table with one or more local secondary indexes, describe the indexes on the table, and perform queries using the indexes\. These operations map to the corresponding low\-level DynamoDB API actions\. For more information, see [\.NET code examples](CodeSamples.DotNet.md)\. 

The following are the common steps for table operations using the \.NET low\-level API\. 

1. Create an instance of the `AmazonDynamoDBClient` class\.

1. Provide the required and optional parameters for the operation by creating the corresponding request objects\.

   For example, create a `CreateTableRequest` object to create a table and an `QueryRequest` object to query a table or an index\. 

1. Run the appropriate method provided by the client that you created in the preceding step\. 

## Create a table with a Local Secondary Index<a name="LSILowLevelDotNet.CreateTableWithIndex"></a>

Local secondary indexes must be created at the same time that you create a table\. To do this, use `CreateTable` and provide your specifications for one or more local secondary indexes\. The following C\# code example creates a table to hold information about songs in a music collection\. The partition key is `Artist` and the sort key is `SongTitle`\. A secondary index, `AlbumTitleIndex`, facilitates queries by album title\. 

The following are the steps to create a table with a local secondary index, using the \.NET low\-level API\. 

1. Create an instance of the `AmazonDynamoDBClient` class\.

1. Create an instance of the `CreateTableRequest` class to provide the request information\. 

   You must provide the table name, its primary key, and the provisioned throughput values\. For the local secondary index, you must provide the index name, the name and data type of the index sort key, the key schema for the index, and the attribute projection\.

1. Run the `CreateTable` method by providing the request object as a parameter\.

The following C\# code example demonstrates the preceding steps\. The code creates a table \(`Music`\) with a secondary index on the `AlbumTitle` attribute\. The table partition key and sort key, plus the index sort key, are the only attributes projected into the index\.

```
AmazonDynamoDBClient client = new AmazonDynamoDBClient();
string tableName = "Music";

CreateTableRequest createTableRequest = new CreateTableRequest()
{
    TableName = tableName
};

//ProvisionedThroughput
createTableRequest.ProvisionedThroughput = new ProvisionedThroughput()
{
    ReadCapacityUnits = (long)5,
    WriteCapacityUnits = (long)5
};

//AttributeDefinitions
List<AttributeDefinition> attributeDefinitions = new List<AttributeDefinition>();

attributeDefinitions.Add(new AttributeDefinition()
{
    AttributeName = "Artist",
    AttributeType = "S"
});

attributeDefinitions.Add(new AttributeDefinition()
 {
     AttributeName = "SongTitle",
     AttributeType = "S"
 });

attributeDefinitions.Add(new AttributeDefinition()
 {
     AttributeName = "AlbumTitle",
     AttributeType = "S"
 });

createTableRequest.AttributeDefinitions = attributeDefinitions;

//KeySchema
List<KeySchemaElement> tableKeySchema = new List<KeySchemaElement>();

tableKeySchema.Add(new KeySchemaElement() { AttributeName = "Artist", KeyType = "HASH" });  //Partition key
tableKeySchema.Add(new KeySchemaElement() { AttributeName = "SongTitle", KeyType = "RANGE" });  //Sort key

createTableRequest.KeySchema = tableKeySchema;

List<KeySchemaElement> indexKeySchema = new List<KeySchemaElement>();
indexKeySchema.Add(new KeySchemaElement() { AttributeName = "Artist", KeyType = "HASH" });  //Partition key
indexKeySchema.Add(new KeySchemaElement() { AttributeName = "AlbumTitle", KeyType = "RANGE" });  //Sort key

Projection projection = new Projection() { ProjectionType = "INCLUDE" };

List<string> nonKeyAttributes = new List<string>();
nonKeyAttributes.Add("Genre");
nonKeyAttributes.Add("Year");
projection.NonKeyAttributes = nonKeyAttributes;

LocalSecondaryIndex localSecondaryIndex = new LocalSecondaryIndex()
{
    IndexName = "AlbumTitleIndex",
    KeySchema = indexKeySchema,
    Projection = projection
};

List<LocalSecondaryIndex> localSecondaryIndexes = new List<LocalSecondaryIndex>();
localSecondaryIndexes.Add(localSecondaryIndex);
createTableRequest.LocalSecondaryIndexes = localSecondaryIndexes;

CreateTableResponse result = client.CreateTable(createTableRequest);
Console.WriteLine(result.CreateTableResult.TableDescription.TableName);
Console.WriteLine(result.CreateTableResult.TableDescription.TableStatus);
```

You must wait until DynamoDB creates the table and sets the table status to `ACTIVE`\. After that, you can begin putting data items into the table\.

## Describe a table with a Local Secondary Index<a name="LSILowLevelDotNet.DescribeTableWithIndex"></a>

To get information about local secondary indexes on a table, use the `DescribeTable` API\. For each index, you can access its name, key schema, and projected attributes\.

The following are the steps to access local secondary index information a table using the \.NET low\-level API\. 

1. Create an instance of the `AmazonDynamoDBClient` class\.

1. Create an instance of the `DescribeTableRequest` class to provide the request information\. You must provide the table name\.

1. Run the `describeTable` method by providing the request object as a parameter\.

The following C\# code example demonstrates the preceding steps\.

**Example**  

```
AmazonDynamoDBClient client = new AmazonDynamoDBClient();
string tableName = "Music";

DescribeTableResponse response = client.DescribeTable(new DescribeTableRequest() { TableName = tableName });
List<LocalSecondaryIndexDescription> localSecondaryIndexes =
    response.DescribeTableResult.Table.LocalSecondaryIndexes;

// This code snippet will work for multiple indexes, even though
// there is only one index in this example.
foreach (LocalSecondaryIndexDescription lsiDescription in localSecondaryIndexes)
{
    Console.WriteLine("Info for index " + lsiDescription.IndexName + ":");

    foreach (KeySchemaElement kse in lsiDescription.KeySchema)
    {
        Console.WriteLine("\t" + kse.AttributeName + ": key type is " + kse.KeyType);
    }

    Projection projection = lsiDescription.Projection;

    Console.WriteLine("\tThe projection type is: " + projection.ProjectionType);

    if (projection.ProjectionType.ToString().Equals("INCLUDE"))
    {
        Console.WriteLine("\t\tThe non-key projected attributes are:");

        foreach (String s in projection.NonKeyAttributes)
        {
            Console.WriteLine("\t\t" + s);
        }

    }
}
```

## Query a Local Secondary Index<a name="LSILowLevelDotNet.QueryAnIndex"></a>

You can use `Query` on a local secondary index in much the same way you `Query` a table\. You must specify the index name, the query criteria for the index sort key, and the attributes that you want to return\. In this example, the index is `AlbumTitleIndex`, and the index sort key is `AlbumTitle`\. 

The only attributes returned are those that have been projected into the index\. You could modify this query to select non\-key attributes too, but this would require table fetch activity that is relatively expensive\. For more information about table fetches, see [Attribute projections](LSI.md#LSI.Projections)

The following are the steps to query a local secondary index using the \.NET low\-level API\. 

1. Create an instance of the `AmazonDynamoDBClient` class\.

1. Create an instance of the `QueryRequest` class to provide the request information\.

1. Run the `query` method by providing the request object as a parameter\.

The following C\# code example demonstrates the preceding steps\.

**Example**  

```
QueryRequest queryRequest = new QueryRequest
{
    TableName = "Music",
    IndexName = "AlbumTitleIndex",
    Select = "ALL_ATTRIBUTES",
    ScanIndexForward = true,
    KeyConditionExpression = "Artist = :v_artist and AlbumTitle = :v_title",
    ExpressionAttributeValues = new Dictionary<string, AttributeValue>()
    {
        {":v_artist",new AttributeValue {S = "Acme Band"}},
        {":v_title",new AttributeValue {S = "Songs About Life"}}
    },
};

QueryResponse response = client.Query(queryRequest);

foreach (var attribs in response.Items)
{
    foreach (var attrib in attribs)
    {
        Console.WriteLine(attrib.Key + " ---> " + attrib.Value.S);
    }
    Console.WriteLine();
}
```