# Batch operations using the AWS SDK for \.NET object persistence model<a name="DotNetDynamoDBContext.BatchOperations"></a>

## Batch write: Putting and deleting multiple items<a name="DotNetDynamoDBContext.BatchWrite"></a>

To put or delete multiple objects from a table in a single request, do the following:
+ Run the `CreateBatchWrite` method of the `DynamoDBContext`, and create an instance of the `BatchWrite` class\.
+ Specify the items that you want to put or delete\.
  + To put one or more items, use either the `AddPutItem` or the `AddPutItems` method\.
  + To delete one or more items, you can specify either the primary key of the item or a client\-side object that maps to the item that you want to delete\. Use the `AddDeleteItem`, `AddDeleteItems`, and the `AddDeleteKey` methods to specify the list of items to delete\.
+ Call the `BatchWrite.Execute` method to put and delete all the specified items from the table\.

**Note**  
When using the object persistence model, you can specify any number of operations in a batch\. However, note that Amazon DynamoDB limits the number of operations in a batch and the total size of the batch in a batch operation\. For more information about the specific limits, see [BatchWriteItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_BatchWriteItem.html)\. If the API detects that your batch write request exceeded the allowed number of write requests or exceeded the maximum allowed HTTP payload size, it breaks the batch into several smaller batches\. Additionally, if a response to a batch write returns unprocessed items, the API automatically sends another batch request with those unprocessed items\.

Suppose that you defined a C\# class `Book` class that maps to the `ProductCatalog` table in DynamoDB\. The following C\# code example uses the `BatchWrite` object to upload two items and delete one item from the `ProductCatalog` table\. 

**Example**  

```
DynamoDBContext context = new DynamoDBContext(client);
var bookBatch = context.CreateBatchWrite<Book>();

// 1. Specify two books to add.
Book book1 = new Book
{
  Id = 902,
  ISBN = "902-11-11-1111",
  ProductCategory = "Book",
  Title = "My book3 in batch write"
};
Book book2 = new Book
{
  Id = 903,
  ISBN = "903-11-11-1111",
  ProductCategory = "Book",
  Title = "My book4 in batch write"
};


bookBatch.AddPutItems(new List<Book> { book1, book2 });

// 2. Specify one book to delete.
bookBatch.AddDeleteKey(111);

bookBatch.Execute();
```

To put or delete objects from multiple tables, do the following:
+ Create one instance of the `BatchWrite` class for each type and specify the items you want to put or delete as described in the preceding section\.
+ Create an instance of `MultiTableBatchWrite` using one of the following methods:
  + Run the `Combine` method on one of the `BatchWrite` objects that you created in the preceding step\. 
  + Create an instance of the `MultiTableBatchWrite` type by providing a list of `BatchWrite` objects\.
  + Run the `CreateMultiTableBatchWrite` method of `DynamoDBContext` and pass in your list of `BatchWrite` objects\.
+ Call the `Execute` method of `MultiTableBatchWrite`, which performs the specified put and delete operations on various tables\.

Suppose that you defined `Forum` and `Thread` C\# classes that map to the `Forum` and `Thread` tables in DynamoDB\. Also, suppose that the `Thread` class has versioning enabled\. Because versioning is not supported when using batch operations, you must explicitly disable versioning as shown in the following C\# code example\. The example uses the `MultiTableBatchWrite` object to perform a multi\-table update\. 

**Example**  

```
DynamoDBContext context = new DynamoDBContext(client);
// Create BatchWrite objects for each of the Forum and Thread classes.
var forumBatch = context.CreateBatchWrite<Forum>();

DynamoDBOperationConfig config = new DynamoDBOperationConfig();
config.SkipVersionCheck = true;
var threadBatch = context.CreateBatchWrite<Thread>(config);

// 1. New Forum item.
Forum newForum = new Forum
{
  Name = "Test BatchWrite Forum",
  Threads = 0
};
forumBatch.AddPutItem(newForum);

// 2. Specify a forum to delete by specifying its primary key.
forumBatch.AddDeleteKey("Some forum");

// 3. New Thread item.
Thread newThread = new Thread
{
  ForumName = "Amazon S3 forum",
  Subject = "My sample question",
  KeywordTags = new List<string> { "Amazon S3", "Bucket" },
   Message = "Message text"
};

threadBatch.AddPutItem(newThread);

// Now run multi-table batch write.
var superBatch = new MultiTableBatchWrite(forumBatch, threadBatch);
superBatch.Execute();
```

For a working example, see [Example: Batch write operation using the AWS SDK for \.NET object persistence model](orm-dotnet-batchoperations-example.md)\.

**Note**  
The DynamoDB batch API limits the number of writes in a batch and also limits the size of the batch\. For more information, see [BatchWriteItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_BatchWriteItem.html)\. When using the \.NET object persistence model API, you can specify any number of operations\. However, if either the number of operations in a batch or the size exceeds the limit, the \.NET API breaks the batch write request into smaller batches and sends multiple batch write requests to DynamoDB\.

## Batch get: Getting multiple items<a name="DotNetDynamoDBContext.BatchGet"></a>

To retrieve multiple items from a table in a single request, do the following:
+ Create an instance of the `CreateBatchGet` class\.
+ Specify a list of primary keys to retrieve\.
+ Call the `Execute` method\. The response returns the items in the `Results` property\.

The following C\# code example retrieves three items from the `ProductCatalog` table\. The items in the result are not necessarily in the same order in which you specified the primary keys\.

**Example**  

```
DynamoDBContext context = new DynamoDBContext(client);
var bookBatch = context.CreateBatchGet<ProductCatalog>();
bookBatch.AddKey(101);
bookBatch.AddKey(102);
bookBatch.AddKey(103);
bookBatch.Execute();
// Process result.
Console.WriteLine(bookBatch.Results.Count);
Book book1 = bookBatch.Results[0];
Book book2 = bookBatch.Results[1];
Book book3 = bookBatch.Results[2];
```

To retrieve objects from multiple tables, do the following:
+ For each type, create an instance of the `CreateBatchGet` type and provide the primary key values you want to retrieve from each table\. 
+ Create an instance of the `MultiTableBatchGet` class using one of the following methods:
  + Run the `Combine` method on one of the `BatchGet` objects you created in the preceding step\. 
  + Create an instance of the `MultiBatchGet` type by providing a list of `BatchGet` objects\.
  + Run the `CreateMultiTableBatchGet` method of `DynamoDBContext` and pass in your list of `BatchGet` objects\.
+ Call the `Execute` method of `MultiTableBatchGet`, which returns the typed results in the individual `BatchGet` objects\.

 The following C\# code example retrieves multiple items from the `Order` and `OrderDetail` tables using the `CreateBatchGet` method\. 

**Example**  

```
var orderBatch = context.CreateBatchGet<Order>();
orderBatch.AddKey(101);
orderBatch.AddKey(102);

var orderDetailBatch = context.CreateBatchGet<OrderDetail>();
orderDetailBatch.AddKey(101, "P1");
orderDetailBatch.AddKey(101, "P2");
orderDetailBatch.AddKey(102, "P3");
orderDetailBatch.AddKey(102, "P1");

var orderAndDetailSuperBatch = orderBatch.Combine(orderDetailBatch);
orderAndDetailSuperBatch.Execute();

Console.WriteLine(orderBatch.Results.Count);
Console.WriteLine(orderDetailBatch.Results.Count);

Order order1 = orderBatch.Results[0];
Order order2 = orderDetailBatch.Results[1];
OrderDetail orderDetail1 = orderDetailBatch.Results[0];
```