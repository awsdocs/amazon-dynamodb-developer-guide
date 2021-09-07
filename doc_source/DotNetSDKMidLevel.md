# \.NET: Document Model<a name="DotNetSDKMidLevel"></a>

**Topics**
+ [Operations Not Supported by the Document Model](#MidLevelAPILimitations)
+ [Supported Data Types](MidLevelAPILimitations.SupportedTypes.md)
+ [Working with Items in DynamoDB Using the AWS SDK for \.NET Document Model](WorkingWithItemsDocumentClasses.md)
+ [Getting an Item \- Table\.GetItem](#GetMidLevelDotNet)
+ [Deleting an Item \- Table\.DeleteItem](#DeleteItemMidLevelDotNet)
+ [Updating an Item \- Table\.UpdateItem](#UpdateMidLevelDotNet)
+ [Batch Write \- Putting and Deleting Multiple Items](#BatchWriteMidLevelDotNet)
+ [Example: CRUD Operations Using the AWS SDK for \.NET Document Model](ItemCRUDDotNetDocumentAPI.md)
+ [Example: Batch Operations Using the AWS SDK for \.NET Document Model API](example-batch-operations-net-doc-model.md)
+ [Querying Tables in DynamoDB Using the AWS SDK for \.NET Document Model](QueryAndScanMidLevelDotNet.md)

The AWS SDK for \.NET provides document model classes that wrap some of the low\-level Amazon DynamoDB operations, further simplifying your coding\. In the document model, the primary classes are `Table` and `Document`\. The `Table` class provides data operation methods such as `PutItem`, `GetItem`, and `DeleteItem`\. It also provides the `Query` and the `Scan` methods\. The `Document` class represents a single item in a table\.

The preceding document model classes are available in the `Amazon.DynamoDBv2.DocumentModel` namespace\.

## Operations Not Supported by the Document Model<a name="MidLevelAPILimitations"></a>

You can't use the document model classes to create, update, and delete tables\. However, the document model does support most common data operations\.

## Getting an Item \- Table\.GetItem<a name="GetMidLevelDotNet"></a>

The `GetItem` operation retrieves an item as a `Document` instance\. You must provide the primary key of the item that you want to retrieve as shown in the following C\# code example\.

**Example**  

```
Table table = Table.LoadTable(client, "ProductCatalog");
Document document = table.GetItem(101); // Primary key 101.
```

The `GetItem` operation returns all the attributes of the item and performs an eventually consistent read \(see [Read Consistency](HowItWorks.ReadConsistency.md)\) by default\.

### Specifying Optional Parameters<a name="GetMidLevelDotNetOptions"></a>

You can configure additional options for the `GetItem` operation by adding the `GetItemOperationConfig` parameter\. For a complete list of optional parameters, see [GetItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_GetItem.html)\. The following C\# code example retrieves an item from the `ProductCatalog` table\. It specifies the `GetItemOperationConfig` to provide the following optional parameters:
+ The `AttributesToGet` parameter to retrieve only the specified attributes\.
+ The `ConsistentRead` parameter to request the latest values for all the specified attributes\. To learn more about data consistency, see [Read Consistency](HowItWorks.ReadConsistency.md)\.

**Example**  

```
Table table = Table.LoadTable(client, "ProductCatalog");

GetItemOperationConfig config = new GetItemOperationConfig()
{
  AttributesToGet = new List<string>() { "Id", "Title", "Authors", "InStock", "QuantityOnHand" },
  ConsistentRead = true
};
Document doc = table.GetItem(101, config);
```

When you retrieve an item using the document model API, you can access individual elements within the `Document` object is returned, as shown in the following example\.

**Example**  

```
int id = doc["Id"].AsInt();
string title = doc["Title"].AsString();
List<string> authors = doc["Authors"].AsListOfString();
bool inStock = doc["InStock"].AsBoolean();
DynamoDBNull quantityOnHand = doc["QuantityOnHand"].AsDynamoDBNull();
```

For attributes that are of type `List` or `Map`, here is how to map these attributes to the document model API:
+ `List` — Use the `AsDynamoDBList` method\. 
+ `Map` — Use the `AsDocument` method\.

The following code example shows how to retrieve a `List` \(RelatedItems\) and a `Map` \(Pictures\) from the `Document` object:

**Example**  

```
DynamoDBList relatedItems = doc["RelatedItems"].AsDynamoDBList();

Document pictures = doc["Pictures"].AsDocument();
```

## Deleting an Item \- Table\.DeleteItem<a name="DeleteItemMidLevelDotNet"></a>

The `DeleteItem` operation deletes an item from a table\. You can pass the item's primary key as a parameter\. Or, if you've already read an item and have the corresponding `Document` object, you can pass it as a parameter to the `DeleteItem` method, as shown in the following C\# code example\. 

**Example**  

```
Table table = Table.LoadTable(client, "ProductCatalog");

// Retrieve a book (a Document instance)
Document document = table.GetItem(111);

// 1) Delete using the Document instance.
 table.DeleteItem(document);

// 2) Delete using the primary key.
int partitionKey = 222;
table.DeleteItem(partitionKey)
```

### Specifying Optional Parameters<a name="DeleteItemMidLevelDotNetOptions"></a>

You can configure additional options for the `Delete` operation by adding the `DeleteItemOperationConfig` parameter\. For a complete list of optional parameters, see [DeleteTable](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DeleteTable.html)\. The following C\# code example specifies the two following optional parameters:
+  The `ConditionalExpression` parameter to ensure that the book item being deleted has a specific value for the ISBN attribute\. 
+ The `ReturnValues` parameter to request that the `Delete` method return the item that it deleted\. 

**Example**  

```
Table table = Table.LoadTable(client, "ProductCatalog");
int partitionKey = 111;

Expression expr = new Expression();
expr.ExpressionStatement = "ISBN = :val";
expr.ExpressionAttributeValues[":val"] = "11-11-11-11";

// Specify optional parameters for Delete operation.
DeleteItemOperationConfig config = new DeleteItemOperationConfig
{
     ConditionalExpression = expr,
     ReturnValues = ReturnValues.AllOldAttributes // This is the only supported value when using the document model.
};

// Delete the book.
Document d = table.DeleteItem(partitionKey, config);
```

## Updating an Item \- Table\.UpdateItem<a name="UpdateMidLevelDotNet"></a>

The `UpdateItem` operation updates an existing item if it is present\. If the item that has the specified primary key is not found, the `UpdateItem` operation adds a new item\.

You can use the `UpdateItem` operation to update existing attribute values, add new attributes to the existing collection, or delete attributes from the existing collection\. You provide these updates by creating a `Document` instance that describes the updates that you want to perform\. 

The `UpdateItem` action uses the following guidelines:
+ If the item does not exist, `UpdateItem` adds a new item using the primary key that is specified in the input\.
+ If the item exists, `UpdateItem` applies the updates as follows:
  + Replaces the existing attribute values with the values in the update\.
  + If an attribute that you provide in the input does not exist, it adds a new attribute to the item\.
  + If the input attribute value is null, it deletes the attributes, if it is present\. 

**Note**  
This midlevel `UpdateItem` operation does not support the `Add` action \(see [UpdateItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_UpdateItem.html)\) that is supported by the underlying DynamoDB operation\. 

**Note**  
The `PutItem` operation \([Putting an Item \- Table\.PutItem Method](WorkingWithItemsDocumentClasses.md#PutMidLevelDotNet)\) can also perform an update\. If you call `PutItem` to upload an item and the primary key exists, the `PutItem` operation replaces the entire item\. If there are attributes in the existing item and those attributes are not specified on the `Document` that is being put, the `PutItem` operation deletes those attributes\. However, `UpdateItem` only updates the specified input attributes\. Any other existing attributes of that item remain unchanged\. 



The following are the steps to update an item using the AWS SDK for \.NET document model:

1. Run the `Table.LoadTable` method by providing the name of the table in which you want to perform the update operation\.

1. Create a `Document` instance by providing all the updates that you want to perform\. 

   To delete an existing attribute, specify the attribute value as null\. 

1. Call the `Table.UpdateItem` method and provide the `Document` instance as an input parameter\. 

   You must provide the primary key either in the `Document` instance or explicitly as a parameter\.

The following C\# code example demonstrates the preceding tasks\. The code example updates an item in the `Book` table\. The `UpdateItem` operation updates the existing `Authors` attribute, deletes the `PageCount` attribute, and adds a new `XYZ` attribute\. The `Document` instance includes the primary key of the book to update\.

**Example**  

```
Table table = Table.LoadTable(client, "ProductCatalog");

var book = new Document();

// Set the attributes that you wish to update.
book["Id"] = 111; // Primary key.
// Replace the authors attribute.
book["Authors"] = new List<string> { "Author x", "Author y" };
// Add a new attribute.
book["XYZ"] = 12345;
// Delete the existing PageCount attribute.
book["PageCount"] = null;

table.Update(book);
```

### Specifying Optional Parameters<a name="UpdateMidLevelDotNetOptions"></a>

You can configure additional options for the `UpdateItem` operation by adding the `UpdateItemOperationConfig` parameter\. For a complete list of optional parameters, see [UpdateItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_UpdateItem.html)\. 

The following C\# code example updates a book item price to 25\. It specifies the two following optional parameters:
+  The `ConditionalExpression` parameter that identifies the `Price` attribute with value `20` that you expect to be present\. 
+ The `ReturnValues` parameter to request the `UpdateItem` operation to return the item that is updated\. 

**Example**  

```
Table table = Table.LoadTable(client, "ProductCatalog");
string partitionKey = "111";

var book = new Document();
book["Id"] = partitionKey;
book["Price"] = 25;

Expression expr = new Expression();
expr.ExpressionStatement = "Price = :val";
expr.ExpressionAttributeValues[":val"] = "20";

UpdateItemOperationConfig config = new UpdateItemOperationConfig()
{
    ConditionalExpression = expr,
    ReturnValues = ReturnValues.AllOldAttributes
};

Document d1 = table.Update(book, config);
```

## Batch Write \- Putting and Deleting Multiple Items<a name="BatchWriteMidLevelDotNet"></a>

*Batch write* refers to putting and deleting multiple items in a batch\. The operation enables you to put and delete multiple items from one or more tables in a single call\. The following are the steps to put or delete multiple items from a table using the AWS SDK for \.NET document model API\.

1. Create a `Table` object by executing the `Table.LoadTable` method by providing the name of the table in which you want to perform the batch operation\.

1. Run the `CreateBatchWrite` method on the table instance you created in the preceding step and create a `DocumentBatchWrite` object\.

1. Use the `DocumentBatchWrite` object methods to specify the documents that you want to upload or delete\. 

1. Call the `DocumentBatchWrite.Execute` method to run the batch operation\.

   When using the document model API, you can specify any number of operations in a batch\. However, DynamoDB limits the number of operations in a batch and the total size of the batch in a batch operation\. For more information about the specific limits, see [BatchWriteItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_BatchWriteItem.html)\. If the document model API detects that your batch write request exceeded the number of allowed write requests, or the HTTP payload size of a batch exceeded the limit allowed by `BatchWriteItem`, it breaks the batch into several smaller batches\. Additionally, if a response to a batch write returns unprocessed items, the document model API automatically sends another batch request with those unprocessed items\.

The following C\# code example demonstrates the preceding steps\. The example uses batch write operation to perform two writes; upload a book item and delete another book item\.

```
Table productCatalog = Table.LoadTable(client, "ProductCatalog");
var batchWrite = productCatalog.CreateBatchWrite();

var book1 = new Document();
book1["Id"] = 902;
book1["Title"] = "My book1 in batch write using .NET document model";
book1["Price"] = 10;
book1["Authors"] = new List<string> { "Author 1", "Author 2", "Author 3" };
book1["InStock"] = new DynamoDBBool(true);
book1["QuantityOnHand"] = 5;

batchWrite.AddDocumentToPut(book1);
// specify delete item using overload that takes PK.
batchWrite.AddKeyToDelete(12345);

batchWrite.Execute();
```

For a working example, see [Example: Batch Operations Using the AWS SDK for \.NET Document Model API](example-batch-operations-net-doc-model.md)\. 

You can use the `batchWrite` operation to perform put and delete operations on multiple tables\. The following are the steps to put or delete multiple items from multiple tables using the AWS SDK for \.NET document model\.

1. You create a `DocumentBatchWrite` instance for each table in which you want to put or delete multiple items, as described in the preceding procedure\.

1. Create an instance of the `MultiTableDocumentBatchWrite` and add the individual `DocumentBatchWrite` objects to it\.

1. Run the `MultiTableDocumentBatchWrite.Execute` method\.

The following C\# code example demonstrates the preceding steps\. The example uses the batch write operation to perform the following write operations:
+ Put a new item in the `Forum` table item\. 
+ Put an item in the `Thread` table and delete an item from the same table\.

```
// 1. Specify item to add in the Forum table.
Table forum = Table.LoadTable(client, "Forum");
var forumBatchWrite = forum.CreateBatchWrite();

var forum1 = new Document();
forum1["Name"] = "Test BatchWrite Forum";
forum1["Threads"] = 0;
forumBatchWrite.AddDocumentToPut(forum1);


// 2a. Specify item to add in the Thread table.
Table thread = Table.LoadTable(client, "Thread");
var threadBatchWrite = thread.CreateBatchWrite();

var thread1 = new Document();
thread1["ForumName"] = "Amazon S3 forum";
thread1["Subject"] = "My sample question";
thread1["Message"] = "Message text";
thread1["KeywordTags"] = new List<string>{ "Amazon S3", "Bucket" };
threadBatchWrite.AddDocumentToPut(thread1);

// 2b. Specify item to delete from the Thread table.
threadBatchWrite.AddKeyToDelete("someForumName", "someSubject");

// 3. Create multi-table batch.
var superBatch = new MultiTableDocumentBatchWrite();
superBatch.AddBatch(forumBatchWrite);
superBatch.AddBatch(threadBatchWrite);

superBatch.Execute();
```