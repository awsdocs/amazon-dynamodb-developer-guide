# Optimistic Locking Using a Version Number with DynamoDB Using the AWS SDK for \.NET Object Persistence Model<a name="DynamoDBContext.VersionSupport"></a>

Optimistic locking support in the object persistence model ensures that the item version for your application is the same as the item version on the server side before updating or deleting the item\. Suppose that you retrieve an item for update\. However, before you send your updates back, some other application updates the same item\. Now your application has a stale copy of the item\. Without optimistic locking, any update you perform will overwrite the update made by the other application\. 

The optimistic locking feature of the object persistence model provides the `DynamoDBVersion` tag that you can use to enable optimistic locking\. To use this feature, you add a property to your class for storing the version number\. You add the `DynamoDBVersion` attribute to the property\. When you first save the object, the `DynamoDBContext` assigns a version number and increments this value each time you update the item\. 

Your update or delete request succeeds only if the client\-side object version matches the corresponding version number of the item on the server side\. If your application has a stale copy, it must get the latest version from the server before it can update or delete that item\.

The following C\# code example defines a `Book` class with object persistence attributes mapping it to the `ProductCatalog` table\. The `VersionNumber` property in the class decorated with the `DynamoDBVersion` attribute stores the version number value\.

**Example**  

```
[DynamoDBTable("ProductCatalog")]
  public class Book
  {
    [DynamoDBHashKey]   //Partition key
    public int Id { get; set; }
    [DynamoDBProperty]
    public string Title { get; set; }
    [DynamoDBProperty]
    public string ISBN { get; set; }
    [DynamoDBProperty("Authors")]
    public List<string> BookAuthors { get; set; }
    [DynamoDBVersion]
    public int? VersionNumber { get; set; }
  }
```

**Note**  
You can apply the `DynamoDBVersion` attribute only to a nullable numeric primitive type \(such as `int?`\)\. 

Optimistic locking has the following impact on `DynamoDBContext` operations:
+ For a new item, `DynamoDBContext` assigns initial version number 0\. If you retrieve an existing item, update one or more of its properties, and try to save the changes, the save operation succeeds only if the version number on the client side and the server side match\. `DynamoDBContext` increments the version number\. You don't need to set the version number\.
+ The `Delete` method provides overloads that can take either a primary key value or an object as parameter, as shown in the following C\# code example\.  
**Example**  

  ```
  DynamoDBContext context = new DynamoDBContext(client);
  ...
  // Load a book.
  Book book = context.Load<ProductCatalog>(111);
  // Do other operations.
  // Delete 1 - Pass in the book object.
  context.Delete<ProductCatalog>(book);
  
  // Delete 2 - Pass in the Id (primary key)
  context.Delete<ProductCatalog>(222);
  ```

  If you provide an object as the parameter, the delete succeeds only if the object version matches the corresponding server\-side item version\. However, if you provide a primary key value as the parameter, `DynamoDBContext` is unaware of any version numbers, and it deletes the item without making the version check\. 

  Note that the internal implementation of optimistic locking in the object persistence model code uses the conditional update and the conditional delete API actions in DynamoDB\.

## Disabling Optimistic Locking<a name="DotNetDynamoDBContext.DisablingOptimisticLocking"></a>

To disable optimistic locking, you use the `SkipVersionCheck` configuration property\. You can set this property when creating `DynamoDBContext`\. In this case, optimistic locking is disabled for any requests that you make using the context\. For more information, see [Specifying Optional Parameters for DynamoDBContext ](DotNetDynamoDBContext.md#OptionalConfigParams)\. 

Instead of setting the property at the context level, you can disable optimistic locking for a specific operation, as shown in the following C\# code example\. The example uses the context to delete a book item\. The `Delete` method sets the optional `SkipVersionCheck` property to true, disabling version checking\.

**Example**  

```
DynamoDBContext context = new DynamoDBContext(client);
// Load a book.
Book book = context.Load<ProductCatalog>(111);
...
// Delete the book.
context.Delete<Book>(book, new DynamoDBContextConfig { SkipVersionCheck = true });
```