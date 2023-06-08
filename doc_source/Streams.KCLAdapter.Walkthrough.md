# Walkthrough: DynamoDB Streams Kinesis adapter<a name="Streams.KCLAdapter.Walkthrough"></a>

This section is a walkthrough of a Java application that uses the Amazon Kinesis Client Library and the Amazon DynamoDB Streams Kinesis Adapter\. The application shows an example of data replication, in which write activity from one table is applied to a second table, with both tables' contents staying in sync\. For the source code, see [Complete program: DynamoDB Streams Kinesis adapter](Streams.KCLAdapter.Walkthrough.CompleteProgram.md)\.

The program does the following:

1. Creates two DynamoDB tables named `KCL-Demo-src` and `KCL-Demo-dst`\. Each of these tables has a stream enabled on it\.

1. Generates update activity in the source table by adding, updating, and deleting items\. This causes data to be written to the table's stream\.

1. Reads the records from the stream, reconstructs them as DynamoDB requests, and applies the requests to the destination table\.

1. Scans the source and destination tables to ensure that their contents are identical\.

1. Cleans up by deleting the tables\.

These steps are described in the following sections, and the complete application is shown at the end of the walkthrough\.

**Topics**
+ [Step 1: Create DynamoDB tables](#Streams.KCLAdapter.Walkthrough.Step1)
+ [Step 2: Generate update activity in source table](#Streams.KCLAdapter.Walkthrough.Step2)
+ [Step 3: Process the stream](#Streams.KCLAdapter.Walkthrough.Step3)
+ [Step 4: Ensure that both tables have identical contents](#Streams.KCLAdapter.Walkthrough.Step4)
+ [Step 5: Clean up](#Streams.KCLAdapter.Walkthrough.Step5)
+ [Complete program: DynamoDB Streams Kinesis adapter](Streams.KCLAdapter.Walkthrough.CompleteProgram.md)

## Step 1: Create DynamoDB tables<a name="Streams.KCLAdapter.Walkthrough.Step1"></a>

The first step is to create two DynamoDB tables—a source table and a destination table\. The `StreamViewType` on the source table's stream is `NEW_IMAGE`\. This means that whenever an item is modified in this table, the item's "after" image is written to the stream\. In this way, the stream keeps track of all write activity on the table\.

The following example shows the code that is used for creating both tables\.

```
java.util.List<AttributeDefinition> attributeDefinitions = new ArrayList<AttributeDefinition>();
attributeDefinitions.add(new AttributeDefinition().withAttributeName("Id").withAttributeType("N"));

java.util.List<KeySchemaElement> keySchema = new ArrayList<KeySchemaElement>();
keySchema.add(new KeySchemaElement().withAttributeName("Id").withKeyType(KeyType.HASH)); // Partition
                                                                                         // key

ProvisionedThroughput provisionedThroughput = new ProvisionedThroughput().withReadCapacityUnits(2L)
    .withWriteCapacityUnits(2L);

StreamSpecification streamSpecification = new StreamSpecification();
streamSpecification.setStreamEnabled(true);
streamSpecification.setStreamViewType(StreamViewType.NEW_IMAGE);
CreateTableRequest createTableRequest = new CreateTableRequest().withTableName(tableName)
    .withAttributeDefinitions(attributeDefinitions).withKeySchema(keySchema)
    .withProvisionedThroughput(provisionedThroughput).withStreamSpecification(streamSpecification);
```

## Step 2: Generate update activity in source table<a name="Streams.KCLAdapter.Walkthrough.Step2"></a>

The next step is to generate some write activity on the source table\. While this activity is taking place, the source table's stream is also updated in near\-real time\.

The application defines a helper class with methods that call the `PutItem`, `UpdateItem`, and `DeleteItem` API operations for writing the data\. The following code example shows how these methods are used\.

```
StreamsAdapterDemoHelper.putItem(dynamoDBClient, tableName, "101", "test1");
StreamsAdapterDemoHelper.updateItem(dynamoDBClient, tableName, "101", "test2");
StreamsAdapterDemoHelper.deleteItem(dynamoDBClient, tableName, "101");
StreamsAdapterDemoHelper.putItem(dynamoDBClient, tableName, "102", "demo3");
StreamsAdapterDemoHelper.updateItem(dynamoDBClient, tableName, "102", "demo4");
StreamsAdapterDemoHelper.deleteItem(dynamoDBClient, tableName, "102");
```

## Step 3: Process the stream<a name="Streams.KCLAdapter.Walkthrough.Step3"></a>

Now the program begins processing the stream\. The DynamoDB Streams Kinesis Adapter acts as a transparent layer between the KCL and the DynamoDB Streams endpoint, so that the code can fully use KCL rather than having to make low\-level DynamoDB Streams calls\. The program performs the following tasks:
+ It defines a record processor class, `StreamsRecordProcessor`, with methods that comply with the KCL interface definition: `initialize`, `processRecords`, and `shutdown`\. The `processRecords` method contains the logic required for reading from the source table's stream and writing to the destination table\.
+ It defines a class factory for the record processor class \(`StreamsRecordProcessorFactory`\)\. This is required for Java programs that use the KCL\.
+ It instantiates a new KCL `Worker`, which is associated with the class factory\.
+ It shuts down the `Worker` when record processing is complete\.

To learn more about the KCL interface definition, see [Developing consumers using the Kinesis client library](https://docs.aws.amazon.com/kinesis/latest/dev/developing-consumers-with-kcl.html) in the *Amazon Kinesis Data Streams Developer Guide*\. 

The following code example shows the main loop in `StreamsRecordProcessor`\. The `case` statement determines what action to perform, based on the `OperationType` that appears in the stream record\.

```
for (Record record : records) {
    String data = new String(record.getData().array(), Charset.forName("UTF-8"));
    System.out.println(data);
    if (record instanceof RecordAdapter) {
                com.amazonaws.services.dynamodbv2.model.Record streamRecord = ((RecordAdapter) record)
                    .getInternalObject();

                switch (streamRecord.getEventName()) {
                    case "INSERT":
                    case "MODIFY":
                        StreamsAdapterDemoHelper.putItem(dynamoDBClient, tableName,
                            streamRecord.getDynamodb().getNewImage());
                        break;
                    case "REMOVE":
                        StreamsAdapterDemoHelper.deleteItem(dynamoDBClient, tableName,
                            streamRecord.getDynamodb().getKeys().get("Id").getN());
                }
    }
    checkpointCounter += 1;
    if (checkpointCounter % 10 == 0) {
        try {
            checkpointer.checkpoint();
        }
        catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

## Step 4: Ensure that both tables have identical contents<a name="Streams.KCLAdapter.Walkthrough.Step4"></a>

At this point, the source and destination tables' contents are in sync\. The application issues `Scan` requests against both tables to verify that their contents are, in fact, identical\.

The `DemoHelper` class contains a `ScanTable` method that calls the low\-level `Scan` API\. The following example shows how this is used\.

```
if (StreamsAdapterDemoHelper.scanTable(dynamoDBClient, srcTable).getItems()
    .equals(StreamsAdapterDemoHelper.scanTable(dynamoDBClient, destTable).getItems())) {
    System.out.println("Scan result is equal.");
}
else {
    System.out.println("Tables are different!");
}
```

## Step 5: Clean up<a name="Streams.KCLAdapter.Walkthrough.Step5"></a>

The demo is complete, so the application deletes the source and destination tables\. See the following code example\. Even after the tables are deleted, their streams remain available for up to 24 hours, after which they are automatically deleted\.

```
dynamoDBClient.deleteTable(new DeleteTableRequest().withTableName(srcTable));
dynamoDBClient.deleteTable(new DeleteTableRequest().withTableName(destTable));
```