# Using Global Secondary Index write sharding for selective table queries<a name="bp-indexes-gsi-sharding"></a>

Applications frequently need to identify a small subset of items in an Amazon DynamoDB table that meet a certain condition\. When these items are distributed randomly across the partition keys of the table, you could resort to a table scan to retrieve them\. This option can be expensive, but it works well when a large number of items on the table meet the search condition\. However, when the key space is large and the search condition is very selective, this strategy can cause a lot of unnecessary processing\.

To enable selective queries across the entire key space, you can use write sharding by adding an attribute containing a `(0-N)` value to every item that you will use for the global secondary index partition key\. 

The following is an example of a schema that uses this in a Critical\-Event workflow:

![\[Selective queries example.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/SelectiveTableQueries.png)

Using this schema design, the event items are distributed across `0-N` partitions on the GSI, allowing a scatter read using a sort condition on the composite key to retrieve all items with a given state during a specified time period\.

This schema pattern delivers a highly selective result set at minimal cost, without requiring a table scan\.