# Working with Local Secondary Indexes: Java<a name="LSIJavaDocumentAPI"></a>

You can use the AWS SDK for Java Document API to create an Amazon DynamoDB table with one or more local secondary indexes, describe the indexes on the table, and perform queries using the indexes\.

The following are the common steps for table operations using the AWS SDK for Java Document API\.

1. Create an instance of the `DynamoDB` class\.

1. Provide the required and optional parameters for the operation by creating the corresponding request objects\. 

1. Call the appropriate method provided by the client that you created in the preceding step\. 

**Topics**
+ [Create a table with a Local Secondary Index](#LSIJavaDocumentAPI.CreateTableWithIndex)
+ [Describe a table with a Local Secondary Index](#LSIJavaDocumentAPI.DescribeTableWithIndex)
+ [Query a Local Secondary Index](#LSIJavaDocumentAPI.QueryAnIndex)
+ [Example: Local Secondary Indexes using the Java document API](LSIJavaDocumentAPI.Example.md)

## Create a table with a Local Secondary Index<a name="LSIJavaDocumentAPI.CreateTableWithIndex"></a>

Local secondary indexes must be created at the same time you create a table\. To do this, use the `createTable` method and provide your specifications for one or more local secondary indexes\. The following Java code example creates a table to hold information about songs in a music collection\. The partition key is `Artist` and the sort key is `SongTitle`\. A secondary index, `AlbumTitleIndex`, facilitates queries by album title\. 

The following are the steps to create a table with a local secondary index, using the DynamoDB document API\. 

1. Create an instance of the `DynamoDB` class\.

1. Create an instance of the `CreateTableRequest` class to provide the request information\. 

   You must provide the table name, its primary key, and the provisioned throughput values\. For the local secondary index, you must provide the index name, the name and data type for the index sort key, the key schema for the index, and the attribute projection\.

1. Call the `createTable` method by providing the request object as a parameter\.

The following Java code example demonstrates the preceding steps\. The code creates a table \(`Music`\) with a secondary index on the `AlbumTitle` attribute\. The table partition key and sort key, plus the index sort key, are the only attributes projected into the index\.

```
AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard().build();
DynamoDB dynamoDB = new DynamoDB(client);

String tableName = "Music";

CreateTableRequest createTableRequest = new CreateTableRequest().withTableName(tableName);

//ProvisionedThroughput
createTableRequest.setProvisionedThroughput(new ProvisionedThroughput().withReadCapacityUnits((long)5).withWriteCapacityUnits((long)5));

//AttributeDefinitions
ArrayList<AttributeDefinition> attributeDefinitions= new ArrayList<AttributeDefinition>();
attributeDefinitions.add(new AttributeDefinition().withAttributeName("Artist").withAttributeType("S"));
attributeDefinitions.add(new AttributeDefinition().withAttributeName("SongTitle").withAttributeType("S"));
attributeDefinitions.add(new AttributeDefinition().withAttributeName("AlbumTitle").withAttributeType("S"));

createTableRequest.setAttributeDefinitions(attributeDefinitions);

//KeySchema
ArrayList<KeySchemaElement> tableKeySchema = new ArrayList<KeySchemaElement>();
tableKeySchema.add(new KeySchemaElement().withAttributeName("Artist").withKeyType(KeyType.HASH));  //Partition key
tableKeySchema.add(new KeySchemaElement().withAttributeName("SongTitle").withKeyType(KeyType.RANGE));  //Sort key

createTableRequest.setKeySchema(tableKeySchema);

ArrayList<KeySchemaElement> indexKeySchema = new ArrayList<KeySchemaElement>();
indexKeySchema.add(new KeySchemaElement().withAttributeName("Artist").withKeyType(KeyType.HASH));  //Partition key
indexKeySchema.add(new KeySchemaElement().withAttributeName("AlbumTitle").withKeyType(KeyType.RANGE));  //Sort key

Projection projection = new Projection().withProjectionType(ProjectionType.INCLUDE);
ArrayList<String> nonKeyAttributes = new ArrayList<String>();
nonKeyAttributes.add("Genre");
nonKeyAttributes.add("Year");
projection.setNonKeyAttributes(nonKeyAttributes);

LocalSecondaryIndex localSecondaryIndex = new LocalSecondaryIndex()
    .withIndexName("AlbumTitleIndex").withKeySchema(indexKeySchema).withProjection(projection);

ArrayList<LocalSecondaryIndex> localSecondaryIndexes = new ArrayList<LocalSecondaryIndex>();
localSecondaryIndexes.add(localSecondaryIndex);
createTableRequest.setLocalSecondaryIndexes(localSecondaryIndexes);

Table table = dynamoDB.createTable(createTableRequest);
System.out.println(table.getDescription());
```

You must wait until DynamoDB creates the table and sets the table status to `ACTIVE`\. After that, you can begin putting data items into the table\.

## Describe a table with a Local Secondary Index<a name="LSIJavaDocumentAPI.DescribeTableWithIndex"></a>

To get information about local secondary indexes on a table, use the `describeTable` method\. For each index, you can access its name, key schema, and projected attributes\.

The following are the steps to access local secondary index information of a table using the AWS SDK for Java Document API\.

1. Create an instance of the `DynamoDB` class\.

1. Create an instance of the `Table` class\. You must provide the table name\.

1. Call the `describeTable` method on the `Table` object\.

The following Java code example demonstrates the preceding steps\.

**Example**  

```
AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard().build();
DynamoDB dynamoDB = new DynamoDB(client);

String tableName = "Music";

Table table = dynamoDB.getTable(tableName);

TableDescription tableDescription = table.describe();

List<LocalSecondaryIndexDescription> localSecondaryIndexes 
    = tableDescription.getLocalSecondaryIndexes();

// This code snippet will work for multiple indexes, even though
// there is only one index in this example.

Iterator<LocalSecondaryIndexDescription> lsiIter = localSecondaryIndexes.iterator();
while (lsiIter.hasNext()) {

    LocalSecondaryIndexDescription lsiDescription = lsiIter.next();
    System.out.println("Info for index " + lsiDescription.getIndexName() + ":");
    Iterator<KeySchemaElement> kseIter = lsiDescription.getKeySchema().iterator();
    while (kseIter.hasNext()) {
        KeySchemaElement kse = kseIter.next();
        System.out.printf("\t%s: %s\n", kse.getAttributeName(), kse.getKeyType());
    }
    Projection projection = lsiDescription.getProjection();
    System.out.println("\tThe projection type is: " + projection.getProjectionType());
    if (projection.getProjectionType().toString().equals("INCLUDE")) {
        System.out.println("\t\tThe non-key projected attributes are: " + projection.getNonKeyAttributes());
    }
}
```

## Query a Local Secondary Index<a name="LSIJavaDocumentAPI.QueryAnIndex"></a>

You can use the `Query` operation on a local secondary index in much the same way that you `Query` a table\. You must specify the index name, the query criteria for the index sort key, and the attributes that you want to return\. In this example, the index is `AlbumTitleIndex` and the index sort key is `AlbumTitle`\. 

The only attributes returned are those that have been projected into the index\. You could modify this query to select non\-key attributes too, but this would require table fetch activity that is relatively expensive\. For more information about table fetches, see [Attribute projections](LSI.md#LSI.Projections)\.

The following are the steps to query a local secondary index using the AWS SDK for Java Document API\. 

1. Create an instance of the `DynamoDB` class\.

1. Create an instance of the `Table` class\. You must provide the table name\.

1. Create an instance of the `Index` class\. You must provide the index name\.

1. Call the `query` method of the `Index` class\.

The following Java code example demonstrates the preceding steps\.

**Example**  

```
AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard().build();
DynamoDB dynamoDB = new DynamoDB(client);

String tableName = "Music";

Table table = dynamoDB.getTable(tableName);
Index index = table.getIndex("AlbumTitleIndex");

QuerySpec spec = new QuerySpec()
    .withKeyConditionExpression("Artist = :v_artist and AlbumTitle = :v_title")
    .withValueMap(new ValueMap()
        .withString(":v_artist", "Acme Band")
        .withString(":v_title", "Songs About Life"));

ItemCollection<QueryOutcome> items = index.query(spec);

Iterator<Item> itemsIter = items.iterator();

while (itemsIter.hasNext()) {
    Item item = itemsIter.next();
    System.out.println(item.toJSONPretty());
}
```