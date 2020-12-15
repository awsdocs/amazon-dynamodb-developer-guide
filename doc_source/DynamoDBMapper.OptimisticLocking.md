# Optimistic Locking with Version Number<a name="DynamoDBMapper.OptimisticLocking"></a>

*Optimistic locking* is a strategy to ensure that the client\-side item that you are updating \(or deleting\) is the same as the item in Amazon DynamoDB\. If you use this strategy, your database writes are protected from being overwritten by the writes of others, and vice versa\.

With optimistic locking, each item has an attribute that acts as a version number\. If you retrieve an item from a table, the application records the version number of that item\. You can update the item, but only if the version number on the server side has not changed\. If there is a version mismatch, it means that someone else has modified the item before you did\. The update attempt fails, because you have a stale version of the item\. If this happens, you simply try again by retrieving the item and then trying to update it\. Optimistic locking prevents you from accidentally overwriting changes that were made by others\. It also prevents others from accidentally overwriting your changes\.

To support optimistic locking, the AWS SDK for Java provides the `@DynamoDBVersionAttribute` annotation\. In the mapping class for your table, you designate one property to store the version number, and mark it using this annotation\. When you save an object, the corresponding item in the DynamoDB table will have an attribute that stores the version number\. The `DynamoDBMapper` assigns a version number when you first save the object, and it automatically increments the version number each time you update the item\. Your update or delete requests succeed only if the client\-side object version matches the corresponding version number of the item in the DynamoDB table\.

 `ConditionalCheckFailedException` is thrown if: 
+  You use optimistic locking with `@DynamoDBVersionAttribute` and the version value on the server is different from the value on the client side\. 
+  You specify your own conditional constraints while saving data by using `DynamoDBMapper` with `DynamoDBSaveExpression` and these constraints failed\. 

**Note**  
DynamoDB global tables use a “last writer wins” reconciliation between concurrent updates\. If you use global tables, last writer policy wins\. So in this case, the locking strategy does not work as expected\.
`DynamoDBMapper` transactional write operations do not support `@DynamoDBVersionAttribute` annotation and condition expressions within the same API call\. If an object within a transactional write is annotated with `@DynamoDBVersionAttribute` and also has a condition expression, then an SdkClientException will be thrown\.

For example, the following Java code defines a `CatalogItem` class that has several properties\. The `Version` property is tagged with the `@DynamoDBVersionAttribute` annotation\.

**Example**  

```
@DynamoDBTable(tableName="ProductCatalog")
public class CatalogItem {

    private Integer id;
    private String title;
    private String ISBN;
    private Set<String> bookAuthors;
    private String someProp;
    private Long version;

    @DynamoDBHashKey(attributeName="Id")
    public Integer getId() { return id; }
    public void setId(Integer Id) { this.id = Id; }

    @DynamoDBAttribute(attributeName="Title")
    public String getTitle() { return title; }
    public void setTitle(String title) { this.title = title; }

    @DynamoDBAttribute(attributeName="ISBN")
    public String getISBN() { return ISBN; }
    public void setISBN(String ISBN) { this.ISBN = ISBN;}

    @DynamoDBAttribute(attributeName = "Authors")
    public Set<String> getBookAuthors() { return bookAuthors; }
    public void setBookAuthors(Set<String> bookAuthors) { this.bookAuthors = bookAuthors; }

    @DynamoDBIgnore
    public String getSomeProp() { return someProp;}
    public void setSomeProp(String someProp) {this.someProp = someProp;}

    @DynamoDBVersionAttribute
    public Long getVersion() { return version; }
    public void setVersion(Long version) { this.version = version;}
}
```

You can apply the `@DynamoDBVersionAttribute` annotation to nullable types provided by the primitive wrappers classes that provide a nullable type, such as `Long` and `Integer`\. 

Optimistic locking has the following impact on these `DynamoDBMapper` methods:
+ `save` — For a new item, the `DynamoDBMapper` assigns an initial version number of 1\. If you retrieve an item, update one or more of its properties, and attempt to save the changes, the save operation succeeds only if the version number on the client side and the server side match\. The `DynamoDBMapper` increments the version number automatically\.
+ `delete` — The `delete` method takes an object as a parameter, and the `DynamoDBMapper` performs a version check before deleting the item\. The version check can be disabled if `DynamoDBMapperConfig.SaveBehavior.CLOBBER` is specified in the request\.

  The internal implementation of optimistic locking within `DynamoDBMapper` uses conditional update and conditional delete support provided by DynamoDB\. 
+ `transactionWrite` —
  + `Put` — For a new item, the `DynamoDBMapper` assigns an initial version number of 1\. If you retrieve an item, update one or more of its properties, and attempt to save the changes, the put operation succeeds only if the version number on the client side and the server side match\. The `DynamoDBMapper` increments the version number automatically\.
  + `Update` — For a new item, the `DynamoDBMapper` assigns an initial version number of 1\. If you retrieve an item, update one or more of its properties, and attempt to save the changes, the update operation succeeds only if the version number on the client side and the server side match\. The `DynamoDBMapper` increments the version number automatically\.
  + `Delete` — The `DynamoDBMapper` performs a version check before deleting the item\. The delete operation succeeds only if the version number on the client side and the server side match\.
  + `ConditionCheck` — The `@DynamoDBVersionAttribute` annotation is not supported for `ConditionCheck` operations\. An SdkClientException will be thrown when a `ConditionCheck` item is annotated with `@DynamoDBVersionAttribute`\. 

## Disabling Optimistic Locking<a name="DynamoDBMapper.OptimisticLocking.Disabling"></a>

To disable optimistic locking, you can change the `DynamoDBMapperConfig.SaveBehavior` enumeration value from `UPDATE` to `CLOBBER`\. You can do this by creating a `DynamoDBMapperConfig` instance that skips version checking and use this instance for all your requests\. For information about `DynamoDBMapperConfig.SaveBehavior` and other optional `DynamoDBMapper` parameters, see [Optional Configuration Settings for DynamoDBMapper ](DynamoDBMapper.OptionalConfig.md)\. 

You can also set locking behavior for a specific operation only\. For example, the following Java snippet uses the `DynamoDBMapper` to save a catalog item\. It specifies `DynamoDBMapperConfig.SaveBehavior` by adding the optional `DynamoDBMapperConfig` parameter to the `save` method\. 

**Note**  
The transactionWrite method does not support DynamoDBMapperConfig\.SaveBehavior configuration\. Disabling optimistic locking for transactionWrite is not supported\.

**Example**  

```
DynamoDBMapper mapper = new DynamoDBMapper(client);

// Load a catalog item.
CatalogItem item = mapper.load(CatalogItem.class, 101);
item.setTitle("This is a new title for the item");
...
// Save the item.
mapper.save(item,
    new DynamoDBMapperConfig(
        DynamoDBMapperConfig.SaveBehavior.CLOBBER));
```