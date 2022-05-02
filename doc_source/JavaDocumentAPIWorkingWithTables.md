# Working with DynamoDB Tables in Java<a name="JavaDocumentAPIWorkingWithTables"></a>

You can use the AWS SDK for Java to create, update, and delete Amazon DynamoDB tables, list all the tables in your account, or get information about a specific table\.

**Topics**
+ [Creating a Table](#JavaDocumentAPICreate)
+ [Updating a Table](#JavaDocumentAPIUpdate)
+ [Deleting a Table](#JavaDocumentAPIDelete)
+ [Listing Tables](#JavaDocumentAPIListTables)
+ [Example: Create, Update, Delete, and List Tables Using the AWS SDK for Java Document API](JavaDocumentAPITablesExample.md)

## Creating a Table<a name="JavaDocumentAPICreate"></a>

To create a table, you must provide the table name, its primary key, and the provisioned throughput values\. The following code snippet creates an example table that uses a numeric type attribute ID as its primary key\.

**To create a table using the AWS SDK for Java API**

1. Create an instance of the `DynamoDB` class\.

1. Instantiate a `CreateTableRequest` to provide the request information\. 

   You must provide the table name, attribute definitions, key schema, and provisioned throughput values\.

1. Run the `createTable` method by providing the request object as a parameter\.

The following code example demonstrates the preceding steps\.

```
AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard().build();
DynamoDB dynamoDB = new DynamoDB(client);

List<AttributeDefinition> attributeDefinitions= new ArrayList<AttributeDefinition>();
attributeDefinitions.add(new AttributeDefinition().withAttributeName("Id").withAttributeType("N"));

List<KeySchemaElement> keySchema = new ArrayList<KeySchemaElement>();
keySchema.add(new KeySchemaElement().withAttributeName("Id").withKeyType(KeyType.HASH));

CreateTableRequest request = new CreateTableRequest()
        .withTableName(tableName)
        .withKeySchema(keySchema)
        .withAttributeDefinitions(attributeDefinitions)
        .withProvisionedThroughput(new ProvisionedThroughput()
            .withReadCapacityUnits(5L)
            .withWriteCapacityUnits(6L));

Table table = dynamoDB.createTable(request);

table.waitForActive();
```

The table is not ready for use until DynamoDB creates it and sets its status to *ACTIVE*\. The `createTable` request returns a `Table` object that you can use to obtain more information about the table\.

**Example**  

```
TableDescription tableDescription =
    dynamoDB.getTable(tableName).describe();

System.out.printf("%s: %s \t ReadCapacityUnits: %d \t WriteCapacityUnits: %d",
    tableDescription.getTableStatus(),
    tableDescription.getTableName(),
    tableDescription.getProvisionedThroughput().getReadCapacityUnits(),
    tableDescription.getProvisionedThroughput().getWriteCapacityUnits());
```

You can call the `describe` method of the client to get table information at any time\.

**Example**  

```
TableDescription tableDescription = dynamoDB.getTable(tableName).describe();
```

## Updating a Table<a name="JavaDocumentAPIUpdate"></a>

You can update only the provisioned throughput values of an existing table\. Depending on your application requirements, you might need to update these values\. 

**Note**  
For more information about throughput increases and decreases per day, see [Service, Account, and Table Quotas in Amazon DynamoDB](ServiceQuotas.md)\.

**To update a table using the AWS SDK for Java API**

1. Create an instance of the `Table` class\.

1. Create an instance of the `ProvisionedThroughput` class to provide the new throughput values\.

1. Run the `updateTable` method by providing the `ProvisionedThroughput` instance as a parameter\.

The following code example demonstrates the preceding steps\.

**Example**  

```
AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard().build();
DynamoDB dynamoDB = new DynamoDB(client);

Table table = dynamoDB.getTable("ProductCatalog");

ProvisionedThroughput provisionedThroughput = new ProvisionedThroughput()
    .withReadCapacityUnits(15L)
    .withWriteCapacityUnits(12L);

table.updateTable(provisionedThroughput);

table.waitForActive();
```

## Deleting a Table<a name="JavaDocumentAPIDelete"></a>

**To delete a table using the AWS SDK for Java API**

1. Create an instance of the `Table` class\.

1. Create an instance of the `DeleteTableRequest` class and provide the table name that you want to delete\.

1. Run the `deleteTable` method by providing the `Table` instance as a parameter\.

The following code example demonstrates the preceding steps\.

**Example**  

```
AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard().build();
DynamoDB dynamoDB = new DynamoDB(client);

Table table = dynamoDB.getTable("ProductCatalog");

table.delete();

table.waitForDelete();
```

## Listing Tables<a name="JavaDocumentAPIListTables"></a>

To list tables in your account, create an instance of `DynamoDB` and run the `listTables` method\. The [ListTables](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_ListTables.html) operation requires no parameters\.

**Example**  

```
AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard().build();
DynamoDB dynamoDB = new DynamoDB(client);

TableCollection<ListTablesResult> tables = dynamoDB.listTables();
Iterator<Table> iterator = tables.iterator();

while (iterator.hasNext()) {
    Table table = iterator.next();
    System.out.println(table.getTableName());
}
```