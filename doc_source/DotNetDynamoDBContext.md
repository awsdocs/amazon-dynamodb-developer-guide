# DynamoDBContext class<a name="DotNetDynamoDBContext"></a>

The `DynamoDBContext` class is the entry point to the Amazon DynamoDB database\. It provides a connection to DynamoDB and enables you to access your data in various tables, perform various CRUD operations, and run queries\. The `DynamoDBContext` class provides the following methods\.

## Create​MultiTable​BatchGet<a name="w120aac18c17c21c35b5"></a>

Creates a `MultiTableBatchGet` object, composed of multiple individual `BatchGet` objects\. Each of these `BatchGet` objects can be used for retrieving items from a single DynamoDB table\.

To retrieve the items from tables, use the `ExecuteBatchGet` method, passing the `MultiTableBatchGet` object as a parameter\.

## Create​MultiTable​BatchWrite<a name="w120aac18c17c21c35b7"></a>

Creates a `MultiTableBatchWrite` object, composed of multiple individual `BatchWrite` objects\. Each of these `BatchWrite` objects can be used for writing or deleting items in a single DynamoDB table\.

To write to tables, use the `ExecuteBatchWrite` method, passing the `MultiTableBatchWrite` object as a parameter\.

## Create​batch​get<a name="w120aac18c17c21c35b9"></a>

Creates a `BatchGet` object that you can use to retrieve multiple items from a table\. For more information, see [Batch get: Getting multiple items ](DotNetDynamoDBContext.BatchOperations.md#DotNetDynamoDBContext.BatchGet)\.

## Create​batch​write<a name="w120aac18c17c21c35c11"></a>

Creates a `BatchWrite` object that you can use to put multiple items into a table, or to delete multiple items from a table\. For more information, see [Batch write: Putting and deleting multiple items ](DotNetDynamoDBContext.BatchOperations.md#DotNetDynamoDBContext.BatchWrite)\.

## Delete<a name="w120aac18c17c21c35c13"></a>

Deletes an item from the table\. The method requires the primary key of the item you want to delete\. You can provide either the primary key value or a client\-side object containing a primary key value as a parameter to this method\.
+ If you specify a client\-side object as a parameter and you have enabled optimistic locking, the delete succeeds only if the client\-side and the server\-side versions of the object match\.
+ If you specify only the primary key value as a parameter, the delete succeeds regardless of whether you have enabled optimistic locking or not\.

**Note**  
To perform this operation in the background, use the `DeleteAsync` method instead\.

## Dispose<a name="w120aac18c17c21c35c15"></a>

Disposes of all managed and unmanaged resources\.

## Execute​batch​get<a name="w120aac18c17c21c35c17"></a>

Reads data from one or more tables, processing all of the `BatchGet` objects in a `MultiTableBatchGet`\.

**Note**  
To perform this operation in the background, use the `ExecuteBatchGetAsync` method instead\.

## Execute​batch​write<a name="w120aac18c17c21c35c19"></a>

Writes or deletes data in one or more tables, processing all of the `BatchWrite` objects in a `MultiTableBatchWrite`\.

**Note**  
To perform this operation in the background, use the `ExecuteBatchWriteAsync` method instead\.

## FromDocument<a name="w120aac18c17c21c35c21"></a>

Given an instance of a `Document`, the `FromDocument` method returns an instance of a client\-side class\.

This is helpful if you want to use the document model classes along with the object persistence model to perform any data operations\. For more information about the document model classes provided by the AWS SDK for \.NET, see [\.NET: Document model](DotNetSDKMidLevel.md)\.

Suppose that you have a `Document` object named `doc`, that contains a representation of a `Forum` item\. \(To see how to construct this object, see the description for the `ToDocument` method later in this topic\.\) You can use `FromDocument` to retrieve the `Forum` item from the `Document`, as shown in the following C\# code example\.

**Example**  

```
forum101 = context.FromDocument<Forum>(101);
```

**Note**  
If your `Document` object implements the `IEnumerable` interface, you can use the `FromDocuments` method instead\. This allows you to iterate over all of the class instances in the `Document`\.

## FromQuery<a name="w120aac18c17c21c35c23"></a>

Runs a `Query` operation, with the query parameters defined in a `QueryOperationConfig` object\.

**Note**  
To perform this operation in the background, use the `FromQueryAsync` method instead\.

## FromScan<a name="w120aac18c17c21c35c25"></a>

Runs a `Scan` operation, with the scan parameters defined in a `ScanOperationConfig` object\.

**Note**  
To perform this operation in the background, use the `FromScanAsync` method instead\.

## Get​target​table<a name="w120aac18c17c21c35c27"></a>

Retrieves the target table for the specified type\. This is useful if you are writing a custom converter for mapping arbitrary data to a DynamoDB table, and you need to determine which table is associated with a custom data type\.

## Load<a name="w120aac18c17c21c35c29"></a>

Retrieves an item from a table\. The method requires only the primary key of the item you want to retrieve\. 

By default, DynamoDB returns the item with values that are eventually consistent\. For information about the eventual consistency model, see [Read consistency](HowItWorks.ReadConsistency.md)\.

**Note**  
To perform this operation in the background, use the `LoadAsync` method instead\.

## Query<a name="w120aac18c17c21c35c31"></a>

Queries a table based on query parameters you provide\. 

You can query a table only if it has a composite primary key \(partition key and sort key\)\. When querying, you must specify a partition key and a condition that applies to the sort key\.

Suppose that you have a client\-side `Reply` class mapped to the `Reply` table in DynamoDB\. The following C\# code example queries the `Reply` table to find forum thread replies posted in the past 15 days\. The `Reply` table has a primary key that has the `Id` partition key and the `ReplyDateTime` sort key\. For more information about the `Reply` table, see [Creating tables and loading data for code examples in DynamoDB](SampleData.md)\.

**Example**  

```
DynamoDBContext context = new DynamoDBContext(client);

string replyId = "DynamoDB#DynamoDB Thread 1"; //Partition key
DateTime twoWeeksAgoDate = DateTime.UtcNow.Subtract(new TimeSpan(14, 0, 0, 0)); // Date to compare.
IEnumerable<Reply> latestReplies = context.Query<Reply>(replyId, QueryOperator.GreaterThan, twoWeeksAgoDate);
```

This returns a collection of `Reply` objects\. 

The `Query` method returns a "lazy\-loaded" `IEnumerable` collection\. It initially returns only one page of results, and then makes a service call for the next page if needed\. To obtain all the matching items, you need to iterate only over the `IEnumerable`\.

If your table has a simple primary key \(partition key\), you can't use the `Query` method\. Instead, you can use the `Load` method and provide the partition key to retrieve the item\.

**Note**  
To perform this operation in the background, use the `QueryAsync` method instead\.

## Save<a name="w120aac18c17c21c35c33"></a>

Saves the specified object to the table\. If the primary key specified in the input object doesn't exist in the table, the method adds a new item to the table\. If the primary key exists, the method updates the existing item\. 

If you have optimistic locking configured, the update succeeds only if the client and the server\-side versions of the item match\. For more information, see [Optimistic locking using a version number with DynamoDB using the AWS SDK for \.NET object persistence model](DynamoDBContext.VersionSupport.md)\.

**Note**  
To perform this operation in the background, use the `SaveAsync` method instead\.

## Scan<a name="w120aac18c17c21c35c35"></a>

Performs an entire table scan\. 

You can filter scan results by specifying a scan condition\. The condition can be evaluated on any attributes in the table\. Suppose that you have a client\-side class `Book` mapped to the `ProductCatalog` table in DynamoDB\. The following C\# example scans the table and returns only the book items priced less than 0\.

**Example**  

```
IEnumerable<Book> itemsWithWrongPrice = context.Scan<Book>(
                    new ScanCondition("Price", ScanOperator.LessThan, price),
                    new ScanCondition("ProductCategory", ScanOperator.Equal, "Book")
      );
```

The `Scan` method returns a "lazy\-loaded" `IEnumerable` collection\. It initially returns only one page of results, and then makes a service call for the next page if needed\. To obtain all the matching items, you only need to iterate over the `IEnumerable`\.

For performance reasons, you should query your tables and avoid a table scan\.

**Note**  
To perform this operation in the background, use the `ScanAsync` method instead\.

## ToDocument<a name="w120aac18c17c21c35c37"></a>

Returns an instance of the `Document` document model class from your class instance\. 

This is helpful if you want to use the document model classes along with the object persistence model to perform any data operations\. For more information about the document model classes provided by the AWS SDK for \.NET, see [\.NET: Document model](DotNetSDKMidLevel.md)\. 

Suppose that you have a client\-side class mapped to the sample `Forum` table\. You can then use a `DynamoDBContext` to get an item as a `Document` object from the `Forum` table, as shown in the following C\# code example\.

**Example**  

```
DynamoDBContext context = new DynamoDBContext(client);

Forum forum101 = context.Load<Forum>(101); // Retrieve a forum by primary key.
Document doc = context.ToDocument<Forum>(forum101);
```

## Specifying optional parameters for DynamoDBContext<a name="OptionalConfigParams"></a>

When using the object persistence model, you can specify the following optional parameters for the `DynamoDBContext`\.
+ **`ConsistentRead`—**When retrieving data using the `Load`, `Query`, or `Scan` operations, you can add this optional parameter to request the latest values for the data\.
+ **`IgnoreNullValues`—**This parameter informs `DynamoDBContext` to ignore null values on attributes during a `Save` operation\. If this parameter is false \(or if it is not set\), then a null value is interpreted as a directive to delete the specific attribute\. 
+ **`SkipVersionCheck`—** This parameter informs `DynamoDBContext` not to compare versions when saving or deleting an item\. For more information about versioning, see [Optimistic locking using a version number with DynamoDB using the AWS SDK for \.NET object persistence model](DynamoDBContext.VersionSupport.md)\.
+ **`TableNamePrefix`—** Prefixes all table names with a specific string\. If this parameter is null \(or if it is not set\), then no prefix is used\.

The following C\# example creates a new `DynamoDBContext` by specifying two of the preceding optional parameters\.

**Example**  

```
AmazonDynamoDBClient client = new AmazonDynamoDBClient();
...
DynamoDBContext context =
       new DynamoDBContext(client, new DynamoDBContextConfig { ConsistentRead = true, SkipVersionCheck = true});
```

`DynamoDBContext` includes these optional parameters with each request that you send using this context\. 

Instead of setting these parameters at the `DynamoDBContext` level, you can specify them for individual operations you run using `DynamoDBContext`, as shown in the following C\# code example\. The example loads a specific book item\. The `Load` method of `DynamoDBContext` specifies the preceding optional parameters\. 

**Example**  

```
AmazonDynamoDBClient client = new AmazonDynamoDBClient();
...
DynamoDBContext context = new DynamoDBContext(client);
Book bookItem = context.Load<Book>(productId,new DynamoDBContextConfig{ ConsistentRead = true, SkipVersionCheck = true });
```

In this case, `DynamoDBContext` includes these parameters only when sending the `Get` request\.