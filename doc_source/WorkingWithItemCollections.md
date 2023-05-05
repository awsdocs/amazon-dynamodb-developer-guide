# Item collections \- how to model one\-to\-many relationships in DynamoDB<a name="WorkingWithItemCollections"></a>

In DynamoDB, an *item collection* is a group of items that share the same partition key value, which means the items are related\. Item collections are the primary mechanism to model one\-to\-many relationships in DynamoDB\. Item collections can only exist on tables or indexes configured to use a [composite primary key](HowItWorks.CoreComponents.md#HowItWorks.CoreComponents.PrimaryKey)\.

**Note**  
Item collections can exist either in a base table or a secondary index\. For more information specifically about how item collections interact with indexes, see [Item collections in Local Secondary Indexes](LSI.md#LSI.ItemCollections)\.

Consider the following table showing three different users and their in\-game inventories:

![\[Three different item collections with different attributes.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/item_collection.png)

For some items in each collection, the sort key is a concatenation made up of information used to group data, such as `inventory::armor`, `inventory::weapon` or `info`\. Each item collection can have a different combination of these attributes as the sort key\. User `account1234` has an `inventory::weapons` item, while user `account1387` does not \(because they have not found any yet\)\. User `account1138` only uses two items for their sort key \(since they have no inventory yet\) while the other users use three\.

DynamoDB lets you selectively retrieve items from these item collections to do the following:
+ Retrieve all items from a particular user
+ Retrieve only one item from a particular user
+ Retrieve all the items of a specific type belonging to a particular user

## Speed up queries by organizing your data with item collections<a name="WorkingWithItemCollections.Example"></a>

In this example, each of the items in these three item collections represents a player and the data model we have chosen, based off the game’s and player’s access patterns\. What data does the game need? When does it need it? How frequently does it need it? What’s the cost of doing it this way? These data modeling decisions were made based off the answers to these questions\.

In this game, there is a different page presented to the player for their inventory for weapons and another page for armor\. When the player opens their inventory, weapons are shown first because we want that page to load extremely fast, while subsequent inventory pages can load after that\. Since each of these item types can be quite large as the player acquires more in\-game items, we decided that each inventory page would be its own item in the player’s item collection in the database\. 

The following section talks more about how you can interact with item collections through the `Query` operation\.

**Topics**
+ [Speed up queries by organizing your data with item collections](#WorkingWithItemCollections.Example)
+ [Query operations in DynamoDB](Query.md)