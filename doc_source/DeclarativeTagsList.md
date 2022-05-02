# DynamoDB Attributes<a name="DeclarativeTagsList"></a>

This section describes the attributes that the object persistence model offers so that you can map your classes and properties to DynamoDB tables and attributes\.

**Note**  
In the following attributes, only `DynamoDBTable` and `DynamoDBHashKey` are required\.

## DynamoDBGlobalSecondaryIndexHashKey<a name="w615aac17c17c21c33b7"></a>

Maps a class property to the partition key of a global secondary index\. Use this attribute if you need to `Query` a global secondary index\.

## DynamoDBGlobalSecondaryIndexRangeKey<a name="w615aac17c17c21c33b9"></a>

Maps a class property to the sort key of a global secondary index\. Use this attribute if you need to `Query` a global secondary index and want to refine your results using the index sort key\.

## DynamoDBHashKey<a name="w615aac17c17c21c33c11"></a>

Maps a class property to the partition key of the table's primary key\. The primary key attributes cannot be a collection type\.

The following C\# code example maps the `Book` class to the `ProductCatalog` table, and the `Id` property to the table's primary key partition key\.

```
[DynamoDBTable("ProductCatalog")]
   public class Book {
   [DynamoDBHashKey]
   public int Id { get; set; }

   // Additional properties go here.
}
```

## DynamoDBIgnore<a name="w615aac17c17c21c33c13"></a>

Indicates that the associated property should be ignored\. If you don't want to save any of your class properties, you can add this attribute to instruct `DynamoDBContext` not to include this property when saving objects to the table\.

## DynamoDBLocalSecondaryIndexRangeKey<a name="w615aac17c17c21c33c15"></a>

Maps a class property to the sort key of a local secondary index\. Use this attribute if you need to `Query` a local secondary index and want to refine your results using the index sort key\.

## DynamoDBProperty<a name="w615aac17c17c21c33c17"></a>

Maps a class property to a table attribute\. If the class property maps to a table attribute of the same name, you don't need to specify this attribute\. However, if the names are not the same, you can use this tag to provide the mapping\. In the following C\# statement, the `DynamoDBProperty` maps the `BookAuthors` property to the `Authors` attribute in the table\. 

```
[DynamoDBProperty("Authors")]
public List<string> BookAuthors { get; set; }
```

`DynamoDBContext` uses this mapping information to create the `Authors` attribute when saving object data to the corresponding table\.

## DynamoDBRenamable<a name="w615aac17c17c21c33c19"></a>

Specifies an alternative name for a class property\. This is useful if you are writing a custom converter for mapping arbitrary data to a DynamoDB table where the name of a class property is different from a table attribute\.

## DynamoDBRangeKey<a name="w615aac17c17c21c33c21"></a>

Maps a class property to the sort key of the table's primary key\. If the table has a composite primary key \(partition key and sort key\), you must specify both the `DynamoDBHashKey` and `DynamoDBRangeKey` attributes in your class mapping\.

For example, the sample table `Reply` has a primary key made of the `Id` partition key and `Replenishment` sort key\. The following C\# code example maps the `Reply` class to the `Reply` table\. The class definition also indicates that two of its properties map to the primary key\.

For more information about sample tables, see [Creating Tables and Loading Data for Code Examples in DynamoDB](SampleData.md)\.

```
[DynamoDBTable("Reply")]
public class Reply {
   [DynamoDBHashKey]
   public int ThreadId { get; set; }
   [DynamoDBRangeKey]
   public string Replenishment { get; set; }
   // Additional properties go here.
}
```

## DynamoDBTable<a name="w615aac17c17c21c33c23"></a>

Identifies the target table in DynamoDB to which the class maps\. For example, the following C\# code example maps the `Developer` class to the `People` table in DynamoDB\.

```
[DynamoDBTable("People")]
public class Developer { ...}
```

This attribute can be inherited or overridden\.
+ The `DynamoDBTable` attribute can be inherited\. In the preceding example, if you add a new class, `Lead`, that inherits from the `Developer` class, it also maps to the `People` table\. Both the `Developer` and `Lead` objects are stored in the `People` table\.
+ The `DynamoDBTable` attribute can also be overridden\. In the following C\# code example, the `Manager` class inherits from the `Developer` class\. However, the explicit addition of the `DynamoDBTable` attribute maps the class to another table \(`Managers`\)\.

  ```
  [DynamoDBTable("Managers")]
  public class Manager : Developer { ...}
  ```

 You can add the optional parameter, `LowerCamelCaseProperties`, to request DynamoDB to make the first letter of the property name lowercase when storing the objects to a table, as shown in the following C\# example\.

```
[DynamoDBTable("People", LowerCamelCaseProperties=true)]
public class Developer {
   string DeveloperName;
   ...}
```

When saving instances of the `Developer` class, `DynamoDBContext` saves the `DeveloperName` property as the `developerName`\.

## DynamoDBVersion<a name="w615aac17c17c21c33c25"></a>

Identifies a class property for storing the item version number\. For more information about versioning, see [Optimistic Locking Using a Version Number with DynamoDB Using the AWS SDK for \.NET Object Persistence Model](DynamoDBContext.VersionSupport.md)\.