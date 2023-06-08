# Working with Global Secondary Indexes: Java<a name="GSIJavaDocumentAPI"></a>

You can use the AWS SDK for Java Document API to create an Amazon DynamoDB table with one or more global secondary indexes, describe the indexes on the table, and perform queries using the indexes\. 

The following are the common steps for table operations\. 

1. Create an instance of the `DynamoDB` class\.

1. Provide the required and optional parameters for the operation by creating the corresponding request objects\. 

1. Call the appropriate method provided by the client that you created in the preceding step\. 

**Topics**
+ [Create a table with a Global Secondary Index](#GSIJavaDocumentAPI.CreateTableWithIndex)
+ [Describe a table with a Global Secondary Index](#GSIJavaDocumentAPI.DescribeTableWithIndex)
+ [Query a Global Secondary Index](#GSIJavaDocumentAPI.QueryAnIndex)
+ [Example: Global Secondary Indexes using the AWS SDK for Java document API](GSIJavaDocumentAPI.Example.md)

## Create a table with a Global Secondary Index<a name="GSIJavaDocumentAPI.CreateTableWithIndex"></a>

You can create global secondary indexes at the same time that you create a table\. To do this, use `CreateTable` and provide your specifications for one or more global secondary indexes\. The following Java code example creates a table to hold information about weather data\. The partition key is `Location` and the sort key is `Date`\. A global secondary index named `PrecipIndex` allows fast access to precipitation data for various locations\.

The following are the steps to create a table with a global secondary index, using the DynamoDB document API\. 

1. Create an instance of the `DynamoDB` class\.

1. Create an instance of the `CreateTableRequest` class to provide the request information\.

   You must provide the table name, its primary key, and the provisioned throughput values\. For the global secondary index, you must provide the index name, its provisioned throughput settings, the attribute definitions for the index sort key, the key schema for the index, and the attribute projection\.

1. Call the `createTable` method by providing the request object as a parameter\.

The following Java code example demonstrates the preceding steps\. The code creates a table \(`WeatherData`\) with a global secondary index \(`PrecipIndex`\)\. The index partition key is `Date` and its sort key is `Precipitation`\. All of the table attributes are projected into the index\. Users can query this index to obtain weather data for a particular date, optionally sorting the data by precipitation amount\. 

Because `Precipitation` is not a key attribute for the table, it is not required\. However, `WeatherData` items without `Precipitation` do not appear in `PrecipIndex`\.

```
AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard().build();
DynamoDB dynamoDB = new DynamoDB(client);

// Attribute definitions
ArrayList<AttributeDefinition> attributeDefinitions = new ArrayList<AttributeDefinition>();

attributeDefinitions.add(new AttributeDefinition()
    .withAttributeName("Location")
    .withAttributeType("S"));
attributeDefinitions.add(new AttributeDefinition()
    .withAttributeName("Date")
    .withAttributeType("S"));
attributeDefinitions.add(new AttributeDefinition()
    .withAttributeName("Precipitation")
    .withAttributeType("N"));

// Table key schema
ArrayList<KeySchemaElement> tableKeySchema = new ArrayList<KeySchemaElement>();
tableKeySchema.add(new KeySchemaElement()
    .withAttributeName("Location")
    .withKeyType(KeyType.HASH));  //Partition key
tableKeySchema.add(new KeySchemaElement()
    .withAttributeName("Date")
    .withKeyType(KeyType.RANGE));  //Sort key

// PrecipIndex
GlobalSecondaryIndex precipIndex = new GlobalSecondaryIndex()
    .withIndexName("PrecipIndex")
    .withProvisionedThroughput(new ProvisionedThroughput()
        .withReadCapacityUnits((long) 10)
        .withWriteCapacityUnits((long) 1))
        .withProjection(new Projection().withProjectionType(ProjectionType.ALL));

ArrayList<KeySchemaElement> indexKeySchema = new ArrayList<KeySchemaElement>();

indexKeySchema.add(new KeySchemaElement()
    .withAttributeName("Date")
    .withKeyType(KeyType.HASH));  //Partition key
indexKeySchema.add(new KeySchemaElement()
    .withAttributeName("Precipitation")
    .withKeyType(KeyType.RANGE));  //Sort key

precipIndex.setKeySchema(indexKeySchema);

CreateTableRequest createTableRequest = new CreateTableRequest()
    .withTableName("WeatherData")
    .withProvisionedThroughput(new ProvisionedThroughput()
        .withReadCapacityUnits((long) 5)
        .withWriteCapacityUnits((long) 1))
    .withAttributeDefinitions(attributeDefinitions)
    .withKeySchema(tableKeySchema)
    .withGlobalSecondaryIndexes(precipIndex);

Table table = dynamoDB.createTable(createTableRequest);
System.out.println(table.getDescription());
```

You must wait until DynamoDB creates the table and sets the table status to `ACTIVE`\. After that, you can begin putting data items into the table\.

## Describe a table with a Global Secondary Index<a name="GSIJavaDocumentAPI.DescribeTableWithIndex"></a>

To get information about global secondary indexes on a table, use `DescribeTable`\. For each index, you can access its name, key schema, and projected attributes\.

The following are the steps to access global secondary index information a table\. 

1. Create an instance of the `DynamoDB` class\.

1. Create an instance of the `Table` class to represent the index you want to work with\.

1. Call the `describe` method on the `Table` object\.

The following Java code example demonstrates the preceding steps\.

**Example**  

```
AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard().build();
DynamoDB dynamoDB = new DynamoDB(client);

Table table = dynamoDB.getTable("WeatherData");
TableDescription tableDesc = table.describe();
    

Iterator<GlobalSecondaryIndexDescription> gsiIter = tableDesc.getGlobalSecondaryIndexes().iterator();
while (gsiIter.hasNext()) {
    GlobalSecondaryIndexDescription gsiDesc = gsiIter.next();
    System.out.println("Info for index "
         + gsiDesc.getIndexName() + ":");

    Iterator<KeySchemaElement> kseIter = gsiDesc.getKeySchema().iterator();
    while (kseIter.hasNext()) {
        KeySchemaElement kse = kseIter.next();
        System.out.printf("\t%s: %s\n", kse.getAttributeName(), kse.getKeyType());
    }
    Projection projection = gsiDesc.getProjection();
    System.out.println("\tThe projection type is: "
        + projection.getProjectionType());
    if (projection.getProjectionType().toString().equals("INCLUDE")) {
        System.out.println("\t\tThe non-key projected attributes are: "
            + projection.getNonKeyAttributes());
    }
}
```

## Query a Global Secondary Index<a name="GSIJavaDocumentAPI.QueryAnIndex"></a>

You can use `Query` on a global secondary index, in much the same way you `Query` a table\. You need to specify the index name, the query criteria for the index partition key and sort key \(if present\), and the attributes that you want to return\. In this example, the index is `PrecipIndex`, which has a partition key of `Date` and a sort key of `Precipitation`\. The index query returns all of the weather data for a particular date, where the precipitation is greater than zero\.

The following are the steps to query a global secondary index using the AWS SDK for Java Document API\. 

1. Create an instance of the `DynamoDB` class\.

1. Create an instance of the `Table` class to represent the index you want to work with\.

1. Create an instance of the `Index` class for the index you want to query\.

1. Call the `query` method on the `Index` object\.

The attribute name `Date` is a DynamoDB reserved word\. Therefore, you must use an expression attribute name as a placeholder in the `KeyConditionExpression`\.

The following Java code example demonstrates the preceding steps\.

**Example**  

```
AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard().build();
DynamoDB dynamoDB = new DynamoDB(client);

Table table = dynamoDB.getTable("WeatherData");
Index index = table.getIndex("PrecipIndex");

QuerySpec spec = new QuerySpec()
    .withKeyConditionExpression("#d = :v_date and Precipitation = :v_precip")
    .withNameMap(new NameMap()
        .with("#d", "Date"))
    .withValueMap(new ValueMap()
        .withString(":v_date","2013-08-10")
        .withNumber(":v_precip",0));

ItemCollection<QueryOutcome> items = index.query(spec);
Iterator<Item> iter = items.iterator(); 
while (iter.hasNext()) {
    System.out.println(iter.next().toJSONPretty());
}
```