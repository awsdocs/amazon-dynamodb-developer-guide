# \.NET: Object Persistence Model<a name="DotNetSDKHighLevel"></a>

**Topics**
+ [DynamoDB Attributes](DeclarativeTagsList.md)
+ [DynamoDBContext Class](DotNetDynamoDBContext.md)
+ [Supported Data Types](#DotNetDynamoDBContext.SupportedTypes)
+ [Optimistic Locking Using Version Number with DynamoDB Using the AWS SDK for \.NET Object Persistence Model](DynamoDBContext.VersionSupport.md)
+ [Mapping Arbitrary Data with DynamoDB Using the AWS SDK for \.NET Object Persistence Model](DynamoDBContext.ArbitraryDataMapping.md)
+ [Batch Operations Using the AWS SDK for \.NET Object Persistence Model](DotNetDynamoDBContext.BatchOperations.md)
+ [Example: CRUD Operations Using the AWS SDK for \.NET Object Persistence Model](CRUDHighLevelExample1.md)
+ [Example: Batch Write Operation Using the AWS SDK for \.NET Object Persistence Model](orm-dotnet-batchoperations-example.md)
+ [Example: Query and Scan in DynamoDB Using the AWS SDK for \.NET Object Persistence Model](DynamoDBContext.QueryScan.md)

The AWS SDK for \.NET provides an object persistence model that enables you to map your client\-side classes to Amazon DynamoDB tables\. Each object instance then maps to an item in the corresponding tables\. To save your client\-side objects to the tables, the object persistence model provides the `DynamoDBContext` class, an entry point to DynamoDB\. This class provides you a connection to DynamoDB and enables you to access tables, perform various CRUD operations, and execute queries\. 

The object persistence model provides a set of attributes to map client\-side classes to tables, and properties/fields to table attributes\. 

**Note**  
The object persistence model does not provide an API to create, update, or delete tables\. It provides only data operations\. You can use only the AWS SDK for the \.NET low\-level API to create, update, and delete tables\. For more information, see [Working with Tables: \.NET](LowLevelDotNetWorkingWithTables.md)\.

To show you how the object persistence model works, let's walk through an example\. We'll start with the ProductCatalog table\. It has `Id` as the primary key\. 

```
ProductCatalog(Id, ...)
```

Suppose you have a `Book` class with `Title`, `ISBN`, and `Authors` properties\. You can map the `Book` class to the ProductCatalog table by adding the attributes defined by the object persistence model, as shown in the following C\# code snippet\. 

**Example**  

```
[DynamoDBTable("ProductCatalog")]
  public class Book
  {
    [DynamoDBHashKey]   
    public int Id { get; set; }

    public string Title { get; set; }
    public int ISBN { get; set; }

    [DynamoDBProperty("Authors")]    
    public List<string> BookAuthors { get; set; }

    [DynamoDBIgnore]    
    public string CoverPage { get; set; }
  }
```

In the preceding example, the `DynamoDBTable` attribute maps the `Book` class to the ProductCatalog table\. 

The object persistence model supports both the explicit and default mapping between class properties and table attributes\.
+ **Explicit mapping—**To map a property to a primary key, you must use the `DynamoDBHashKey` and `DynamoDBRangeKey` object persistence model attributes\. Additionally, for the nonprimary key attributes, if a property name in your class and the corresponding table attribute to which you want to map it are not the same, you must define the mapping by explicitly adding the `DynamoDBProperty` attribute\. 

  In the preceding example, the `Id` property maps to the primary key with the same name, and the `BookAuthors` property maps to the `Authors` attribute in the ProductCatalog table\.
+ **Default mapping—**By default, the object persistence model maps the class properties to the attributes with the same name in the table\. 

  In the preceding example, the properties `Title` and `ISBN` map to the attributes with the same name in the ProductCatalog table\. 

You don't have to map every single class property\. You identify these properties by adding the `DynamoDBIgnore` attribute\. When you save a `Book` instance to the table, the `DynamoDBContext` does not include the `CoverPage` property\. It also does not return this property when you retrieve the book instance\. 

You can map properties of \.NET primitive types such as int and string\. You also can map any arbitrary data types as long as you provide an appropriate converter to map the arbitrary data to one of the DynamoDB types\. To learn about mapping arbitrary types, see [Mapping Arbitrary Data with DynamoDB by Using the AWS SDK for \.NET Object Persistence Model](DynamoDBContext.ArbitraryDataMapping.md)\. 

The object persistence model supports optimistic locking\. During an update operation, this ensures you have the latest copy of the item you are about to update\. For more information, see [Optimistic Locking Using Version Number with DynamoDB Using the AWS SDK for \.NET Object Persistence Model](DynamoDBContext.VersionSupport.md)\.

## Supported Data Types<a name="DotNetDynamoDBContext.SupportedTypes"></a>

The object persistence model supports a set of primitive \.NET data types, collections, and arbitrary data types\. The model supports the following primitive data types\. 
+ `bool`
+ `byte` 
+ `char`
+ `DateTime`
+ `decimal`
+ `double`
+ `float`
+ `Int16`
+ `Int32`
+ `Int64`
+ `SByte`
+ `string`
+ `UInt16`
+ `UInt32`
+ `UInt64`

The object persistence model also supports the \.NET collection types\. `DynamoDBContext` is able to convert concrete collection types and simple Plain Old CLR Objects \(POCOs\)\.

The following table summarizes the mapping of the preceding \.NET types to the DynamoDB types\.


****  

| \.NET primitive type | DynamoDB type | 
| --- | --- | 
|  All number types  |  `N` \(number type\)  | 
|  All string types  |  `S` \(string type\)   | 
|  MemoryStream, byte\[\]  |  `B` \(binary type\)   | 
| bool | `N` \(number type\). 0 represents false and 1 represents true\. | 
| Collection types | `BS` \(binary set\) type, `SS` \(string set\) type, and `NS` \(number set\) type | 
| DateTime | `S` \(string type\)\. The DateTime values are stored as ISO\-8601 formatted strings\. | 

The object persistence model also supports arbitrary data types\. However, you must provide converter code to map the complex types to the DynamoDB types\.
