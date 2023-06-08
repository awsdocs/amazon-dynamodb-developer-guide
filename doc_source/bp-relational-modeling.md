# Best practices for modeling relational data in DynamoDB<a name="bp-relational-modeling"></a>

Traditional relational database management system \(RDBMS\) platforms store data in a normalized relational structure\. This structure reduces hierarchical data structures to a set of common elements that are stored across multiple tables\. The following schema is an example of a generic order\-entry application with supporting HR schema backing the operational and business support systems of a theoretical manufacturer\. 

![\[Example RDBMS schema.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/RDBMS.png)

RDBMS platforms use an ad hoc query language \(generally a flavor of SQL\) to generate or materialize views of the normalized data to support application\-layer access patterns\.

For example, to generate a list of purchase order items sorted by the quantity in stock at all warehouses that can ship each item, you could issue the following query against the preceding schema\.

```
SELECT * FROM Orders
  INNER JOIN Order_Items ON Orders.Order_ID = Order_Items.Order_ID
  INNER JOIN Products ON Products.Product_ID = Order_Items.Product_ID
  INNER JOIN Inventories ON Products.Product_ID = Inventories.Product_ID
  ORDER BY Quantity_on_Hand DESC
```

One\-time queries of this kind provide a flexible API for accessing data, but they require a significant amount of processing\. You must often query the data from multiple locations, and the results must be assembled for presentation\. The preceding query initiates complex queries across a number of tables and then sorts and integrates the resulting data\.

Another factor that can slow down RDBMS systems is the need to support an ACID\-compliant transaction framework\. The hierarchical data structures used by most online transaction processing \(OLTP\) applications must be broken down and distributed across multiple logical tables when they are stored in an RDBMS\. Therefore, an ACID\-compliant transaction framework is necessary to avoid race conditions that could occur if an application tries to read an object that is in the process of being written\. Such a transaction framework necessarily adds significant overhead to the write process\.

These two factors are the primary barriers to scale for traditional RDBMS platforms\. It remains to be seen whether the NewSQL community can be successful in delivering a distributed RDBMS solution\. But it is unlikely that even that would resolve the two limitations described earlier\. No matter how the solution is delivered, the processing costs of normalization and ACID transactions must remain significant\. 

For this reason, when your business requires low\-latency response to high\-traffic queries, taking advantage of a NoSQL system generally makes technical and economic sense\. Amazon DynamoDB helps solve the problems that limit relational system scalability by avoiding them\.

A relational database system does not scale well for the following reasons:
+ It normalizes data and stores it on multiple tables that require multiple queries to write to disk\.
+ It generally incurs the performance costs of an ACID\-compliant transaction system\.
+ It uses expensive joins to reassemble required views of query results\.

DynamoDB scales well for these reasons:
+ Schema flexibility lets DynamoDB store complex hierarchical data within a single item\.
+ Composite key design lets it store related items close together on the same table\.

Queries against the data store become much simpler, often in the following form:

```
SELECT * FROM Table_X WHERE Attribute_Y = "somevalue"
```

DynamoDB does far less work to return the requested data compared to the RDBMS in the earlier example\.