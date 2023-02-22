# Working with Global Secondary Indexes: \.NET<a name="GSILowLevelDotNet"></a>

You can use the AWS SDK for \.NET low\-level API to create an Amazon DynamoDB table with one or more global secondary indexes, describe the indexes on the table, and perform queries using the indexes\. These operations map to the corresponding DynamoDB operations\. For more information, see the [Amazon DynamoDB API Reference](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/)\. 

The following are the common steps for table operations using the \.NET low\-level API\. 

1. Create an instance of the `AmazonDynamoDBClient` class\.

1. Provide the required and optional parameters for the operation by creating the corresponding request objects\.

   For example, create a `CreateTableRequest` object to create a table and `QueryRequest` object to query a table or an index\. 

1. Run the appropriate method provided by the client that you created in the preceding step\. 

**Topics**
+ [Create a table with a Global Secondary Index](#GSILowLevelDotNet.CreateTableWithIndex)
+ [Describe a table with a Global Secondary Index](#GSILowLevelDotNet.DescribeTableWithIndex)
+ [Query a Global Secondary Index](#GSILowLevelDotNet.QueryAnIndex)
+ [Example: Global Secondary Indexes using the AWS SDK for \.NET low\-level API](GSILowLevelDotNet.Example.md)

## Create a table with a Global Secondary Index<a name="GSILowLevelDotNet.CreateTableWithIndex"></a>

You can create global secondary indexes at the same time that you create a table\. To do this, use `CreateTable` and provide your specifications for one or more global secondary indexes\. The following C\# code example creates a table to hold information about weather data\. The partition key is `Location` and the sort key is `Date`\. A global secondary index named `PrecipIndex` allows fast access to precipitation data for various locations\.

The following are the steps to create a table with a global secondary index, using the \.NET low\-level API\. 

1. Create an instance of the `AmazonDynamoDBClient` class\.

1. Create an instance of the `CreateTableRequest` class to provide the request information\. 

   You must provide the table name, its primary key, and the provisioned throughput values\. For the global secondary index, you must provide the index name, its provisioned throughput settings, the attribute definitions for the index sort key, the key schema for the index, and the attribute projection\.

1. Run the `CreateTable` method by providing the request object as a parameter\.

The following C\# code example demonstrates the preceding steps\. The code creates a table \(`WeatherData`\) with a global secondary index \(`PrecipIndex`\)\. The index partition key is `Date` and its sort key is `Precipitation`\. All of the table attributes are projected into the index\. Users can query this index to obtain weather data for a particular date, optionally sorting the data by precipitation amount\. 

Because `Precipitation` is not a key attribute for the table, it is not required\. However, `WeatherData` items without `Precipitation` do not appear in `PrecipIndex`\.

```
client = new AmazonDynamoDBClient();
string tableName = "WeatherData";

// Attribute definitions
var attributeDefinitions = new List<AttributeDefinition>()
{
    {new AttributeDefinition{
        AttributeName = "Location",
        AttributeType = "S"}},
    {new AttributeDefinition{
        AttributeName = "Date",
        AttributeType = "S"}},
    {new AttributeDefinition(){
        AttributeName = "Precipitation",
        AttributeType = "N"}
    }
};

// Table key schema
var tableKeySchema = new List<KeySchemaElement>()
{
    {new KeySchemaElement {
        AttributeName = "Location",
        KeyType = "HASH"}},  //Partition key
    {new KeySchemaElement {
        AttributeName = "Date",
        KeyType = "RANGE"}  //Sort key
    }
};

// PrecipIndex
var precipIndex = new GlobalSecondaryIndex
{
    IndexName = "PrecipIndex",
    ProvisionedThroughput = new ProvisionedThroughput
    {
        ReadCapacityUnits = (long)10,
        WriteCapacityUnits = (long)1
    },
    Projection = new Projection { ProjectionType = "ALL" }
};

var indexKeySchema = new List<KeySchemaElement> {
    {new KeySchemaElement { AttributeName = "Date", KeyType = "HASH"}},  //Partition key
    {new KeySchemaElement{AttributeName = "Precipitation",KeyType = "RANGE"}}  //Sort key
};

precipIndex.KeySchema = indexKeySchema;

CreateTableRequest createTableRequest = new CreateTableRequest
{
    TableName = tableName,
    ProvisionedThroughput = new ProvisionedThroughput
    {
        ReadCapacityUnits = (long)5,
        WriteCapacityUnits = (long)1
    },
    AttributeDefinitions = attributeDefinitions,
    KeySchema = tableKeySchema,
    GlobalSecondaryIndexes = { precipIndex }
};

CreateTableResponse response = client.CreateTable(createTableRequest);
Console.WriteLine(response.CreateTableResult.TableDescription.TableName);
Console.WriteLine(response.CreateTableResult.TableDescription.TableStatus);
```

You must wait until DynamoDB creates the table and sets the table status to `ACTIVE`\. After that, you can begin putting data items into the table\.

## Describe a table with a Global Secondary Index<a name="GSILowLevelDotNet.DescribeTableWithIndex"></a>

To get information about global secondary indexes on a table, use `DescribeTable`\. For each index, you can access its name, key schema, and projected attributes\.

The following are the steps to access global secondary index information for a table using the \.NET low\-level API\. 

1. Create an instance of the `AmazonDynamoDBClient` class\.

1. Run the `describeTable` method by providing the request object as a parameter\.

   Create an instance of the `DescribeTableRequest` class to provide the request information\. You must provide the table name\.

The following C\# code example demonstrates the preceding steps\.

**Example**  

```
client = new AmazonDynamoDBClient();
string tableName = "WeatherData";

DescribeTableResponse response = client.DescribeTable(new DescribeTableRequest { TableName = tableName});

List<GlobalSecondaryIndexDescription> globalSecondaryIndexes =
response.DescribeTableResult.Table.GlobalSecondaryIndexes;

// This code snippet will work for multiple indexes, even though
// there is only one index in this example.

foreach (GlobalSecondaryIndexDescription gsiDescription in globalSecondaryIndexes) {
     Console.WriteLine("Info for index " + gsiDescription.IndexName + ":");

     foreach (KeySchemaElement kse in gsiDescription.KeySchema) {
          Console.WriteLine("\t" + kse.AttributeName + ": key type is " + kse.KeyType);
     }

      Projection projection = gsiDescription.Projection;
      Console.WriteLine("\tThe projection type is: " + projection.ProjectionType);

      if (projection.ProjectionType.ToString().Equals("INCLUDE")) {
           Console.WriteLine("\t\tThe non-key projected attributes are: "
                + projection.NonKeyAttributes);
      }
}
```

## Query a Global Secondary Index<a name="GSILowLevelDotNet.QueryAnIndex"></a>

You can use `Query` on a global secondary index, in much the same way you `Query` a table\. You need to specify the index name, the query criteria for the index partition key and sort key \(if present\), and the attributes that you want to return\. In this example, the index is `PrecipIndex`, which has a partition key of `Date` and a sort key of `Precipitation`\. The index query returns all of the weather data for a particular date, where the precipitation is greater than zero\.

The following are the steps to query a global secondary index using the \.NET low\-level API\. 

1. Create an instance of the `AmazonDynamoDBClient` class\.

1. Create an instance of the `QueryRequest` class to provide the request information\.

1. Run the `query` method by providing the request object as a parameter\.

The attribute name `Date` is a DynamoDB reserved word\. Therefore, you must use an expression attribute name as a placeholder in the `KeyConditionExpression`\.

The following C\# code example demonstrates the preceding steps\.

**Example**  

```
client = new AmazonDynamoDBClient();

QueryRequest queryRequest = new QueryRequest
{
    TableName = "WeatherData",
    IndexName = "PrecipIndex",
    KeyConditionExpression = "#dt = :v_date and Precipitation > :v_precip",
    ExpressionAttributeNames = new Dictionary<String, String> {
        {"#dt", "Date"}
    },
    ExpressionAttributeValues = new Dictionary<string, AttributeValue> {
        {":v_date", new AttributeValue { S =  "2013-08-01" }},
        {":v_precip", new AttributeValue { N =  "0" }}
    },
    ScanIndexForward = true
};

var result = client.Query(queryRequest);

var items = result.Items;
foreach (var currentItem in items)
{
    foreach (string attr in currentItem.Keys)
    {
        Console.Write(attr + "---> ");
        if (attr == "Precipitation")
        {
            Console.WriteLine(currentItem[attr].N);
    }
    else
    {
        Console.WriteLine(currentItem[attr].S);
    }

         }
     Console.WriteLine();
}
```