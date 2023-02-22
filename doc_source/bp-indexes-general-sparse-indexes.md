# Take advantage of sparse indexes<a name="bp-indexes-general-sparse-indexes"></a>

For any item in a table, DynamoDB writes a corresponding index entry **only if the index sort key value is present in the item**\. If the sort key doesn't appear in every table item, the index is said to be *sparse*\.

Sparse indexes are useful for queries over a small subsection of a table\. For example, suppose that you have a table where you store all your customer orders, with the following key attributes:
+ Partition key: `CustomerId`
+ Sort key: `OrderId`

To track open orders, you can insert an attribute named `isOpen` in order items that have not already shipped\. Then when the order ships, you can delete the attribute\. If you then create an index on `CustomerId` \(partition key\) and `isOpen` \(sort key\), only those orders with `isOpen` defined appear in it\. When you have thousands of orders of which only a small number are open, it's faster and less expensive to query that index for open orders than to scan the entire table\.

Instead of using a type of attribute like `isOpen`, you could use an attribute with a value that results in a useful sort order in the index\. For example, you could use an `OrderOpenDate` attribute set to the date on which each order was placed, and then delete it after the order is fulfilled\. That way, when you query the sparse index, the items are returned sorted by the date on which each order was placed\.

## Examples of sparse indexes in DynamoDB<a name="bp-indexes-sparse-examples"></a>

Global secondary indexes are sparse by default\. When you create a global secondary index, you specify a partition key and optionally a sort key\. Only items in the base table that contain those attributes appear in the index\.

By designing a global secondary index to be sparse, you can provision it with lower write throughput than that of the base table, while still achieving excellent performance\.

For example, a gaming application might track all scores of every user, but generally only needs to query a few high scores\. The following design handles this scenario efficiently:

![\[Sparse GSI example.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/SparseIndex_A.png)

Here, Rick has played three games and achieved `Champ` status in one of them\. Padma has played four games and achieved `Champ` status in two of them\. Notice that the `Award` attribute is present only in items where the user achieved an award\. The associated global secondary index looks like the following:

![\[Sparse GSI example.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/SparseIndex_B.png)

The global secondary index contains only the high scores that are frequently queried, which are a small subset of the items in the base table\.