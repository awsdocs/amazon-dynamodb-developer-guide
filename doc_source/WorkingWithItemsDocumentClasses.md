# Working with items in DynamoDB using the AWS SDK for \.NET document model<a name="WorkingWithItemsDocumentClasses"></a>

**Topics**
+ [Putting an item \- Table\.PutItem method](#PutMidLevelDotNet)
+ [Specifying optional parameters](#PutMidLevelDotNetOptions)



To perform data operations using the document model, you must first call the `Table.LoadTable` method, which creates an instance of the `Table` class that represents a specific table\. The following C\# example creates a `Table` object that represents the `ProductCatalog` table in Amazon DynamoDB\.

**Example**  

```
Table table = Table.LoadTable(client, "ProductCatalog");
```

**Note**  
In general, you use the `LoadTable` method once at the beginning of your application because it makes a `DescribeTable` call that adds to the round trip to DynamoDB\. 

You can then use the `Table` object to perform various data operations\. Each data operation has two types of overloads: One takes the minimum required parameters and the other takes optional, operation\-specific configuration information\. For example, to retrieve an item, you must provide the table's primary key value, in which case you can use the following `GetItem` overload\.

**Example**  

```
// Get the item from a table that has a primary key that is composed of only a partition key.
Table.GetItem(Primitive partitionKey);
// Get the item from a table whose primary key is composed of both a partition key and sort key.
Table.GetItem(Primitive partitionKey, Primitive sortKey);
```

You also can pass optional parameters to these methods\. For example, the preceding `GetItem` returns the entire item including all its attributes\. You can optionally specify a list of attributes to retrieve\. In this case, you use the following `GetItem` overload that takes in the operation\-specific configuration object parameter\.

**Example**  

```
// Configuration object that specifies optional parameters.
GetItemOperationConfig config = new GetItemOperationConfig()
{
  AttributesToGet = new List<string>() { "Id", "Title" },
};
// Pass in the configuration to the GetItem method.
// 1. Table that has only a partition key as primary key.
Table.GetItem(Primitive partitionKey, GetItemOperationConfig config);
// 2. Table that has both a partition key and a sort key.
Table.GetItem(Primitive partitionKey, Primitive sortKey, GetItemOperationConfig config);
```

You can use the configuration object to specify several optional parameters such as request a specific list of attributes or specify the page size \(number of items per page\)\. Each data operation method has its own configuration class\. For example, you can use the `GetItemOperationConfig` class to provide options for the `GetItem` operation\. You can use the `PutItemOperationConfig` class to provide optional parameters for the `PutItem` operation\. 

The following sections discuss each of the data operations that are supported by the `Table` class\.

## Putting an item \- Table\.PutItem method<a name="PutMidLevelDotNet"></a>

The `PutItem` method uploads the input `Document` instance to the table\. If an item that has a primary key that is specified in the input `Document` exists in the table, the `PutItem` operation replaces the entire existing item\. The new item is identical to the `Document` object that you provided to the `PutItem` method\. If your original item had any extra attributes, they are no longer present in the new item\. 

The following are the steps to put a new item into a table using the AWS SDK for \.NET document model\. 

1. Run the `Table.LoadTable` method that provides the table name in which you want to put an item\.

1. Create a `Document` object that has a list of attribute names and their values\.

1. Run `Table.PutItem` by providing the `Document` instance as a parameter\.

The following C\# code example demonstrates the preceding tasks\. The example uploads an item to the `ProductCatalog` table\. 

**Example**  

```
Table table = Table.LoadTable(client, "ProductCatalog");

var book = new Document();
book["Id"] = 101;
book["Title"] = "Book 101 Title";
book["ISBN"] = "11-11-11-11";
book["Authors"] = new List<string> { "Author 1", "Author 2" };
book["InStock"] = new DynamoDBBool(true);
book["QuantityOnHand"] = new DynamoDBNull();

table.PutItem(book);
```

In the preceding example, the `Document` instance creates an item that has `Number`, `String`, `String Set`, `Boolean`, and `Null` attributes\. \(`Null` is used to indicate that the *QuantityOnHand* for this product is unknown\.\) For `Boolean` and `Null`, use the constructor methods `DynamoDBBool` and `DynamoDBNull`\.

In DynamoDB, the `List` and `Map` data types can contain elements composed of other data types\. Here is how to map these data types to the document model API:
+ List — use the `DynamoDBList` constructor\.
+ Map — use the `Document` constructor\.

You can modify the preceding example to add a `List` attribute to the item\. To do this, use a `DynamoDBList` constructor, as shown in the following code example\.

**Example**  

```
Table table = Table.LoadTable(client, "ProductCatalog");

var book = new Document();
book["Id"] = 101;

/*other attributes omitted for brevity...*/

var relatedItems = new DynamoDBList();
relatedItems.Add(341);
relatedItems.Add(472);
relatedItems.Add(649);
book.Add("RelatedItems", relatedItems);

table.PutItem(book);
```

To add a `Map` attribute to the book, you define another `Document`\. The following code example illustrates how to do this\.

**Example**  

```
Table table = Table.LoadTable(client, "ProductCatalog");

var book = new Document();
book["Id"] = 101;

/*other attributes omitted for brevity...*/

var pictures = new Document();
pictures.Add("FrontView", "http://example.com/products/101_front.jpg" );
pictures.Add("RearView", "http://example.com/products/101_rear.jpg" );

book.Add("Pictures", pictures);

table.PutItem(book);
```

These examples are based on the item shown in [Specifying item attributes when using expressions](Expressions.Attributes.md)\. The document model lets you create complex nested attributes, such as the `ProductReviews` attribute shown in the case study\.

## Specifying optional parameters<a name="PutMidLevelDotNetOptions"></a>

You can configure optional parameters for the `PutItem` operation by adding the `PutItemOperationConfig` parameter\. For a complete list of optional parameters, see [PutItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_PutItem.html)\. The following C\# code example puts an item in the `ProductCatalog` table\. It specifies the following optional parameter:
+  The `ConditionalExpression` parameter to make this a conditional put request\. The example creates an expression that specifies the `ISBN` attribute must have a specific value that has to be present in the item that you are replacing\.

**Example**  

```
Table table = Table.LoadTable(client, "ProductCatalog");

var book = new Document();
book["Id"] = 555;
book["Title"] = "Book 555 Title";
book["Price"] = "25.00";
book["ISBN"] = "55-55-55-55";
book["Name"] = "Item 1 updated";
book["Authors"] = new List<string> { "Author x", "Author y" };
book["InStock"] = new DynamoDBBool(true);
book["QuantityOnHand"] = new DynamoDBNull();

// Create a condition expression for the optional conditional put operation.
Expression expr = new Expression();
expr.ExpressionStatement = "ISBN = :val";
expr.ExpressionAttributeValues[":val"] = "55-55-55-55";

PutItemOperationConfig config = new PutItemOperationConfig()
{
  // Optional parameter.
     ConditionalExpression = expr
};

table.PutItem(book, config);
```