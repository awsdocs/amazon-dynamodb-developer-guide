# Take Advantage of Sparse Indexes<a name="bp-indexes-general-sparse-indexes"></a>

For any item in a table, DynamoDB writes a corresponding index entry **only if the index sort key value is present in the item**\. If the sort key doesn't appear in every table item, the index is said to be *sparse*\.

Sparse indexes are useful for queries over a small subsection of a table\. For example, suppose that you have a table where you store all your customer orders, with the following key attributes:
+ Partition key: `CustomerId`
+ Sort key: `OrderId`

To track open orders, you can insert a Boolean attribute named `isOpen` in order items that have not already shipped\. Then when the order ships, you can delete the attribute\. If you then create an index on `CustomerId` \(partition key\) and `isOpen` \(sort key\), only those orders with `isOpen` defined appear in it\. When you have thousands of orders of which only a small number are open, it's faster and less expensive to query that index for open orders than to scan the entire table\.

Instead of using a Boolean type of attribute like `isOpen`, you could use an attribute with a value that results in a useful sort order in the index\. For example, you could use an `OrderOpenDate` attribute set to the date on which each order was placed, and then delete it after the order is fulfilled\. That way, when you query the sparse index, the items are returned sorted by the date on which each order was placed\.