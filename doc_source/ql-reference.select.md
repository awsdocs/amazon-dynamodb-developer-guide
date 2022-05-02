# PartiQL Select Statements for DynamoDB<a name="ql-reference.select"></a>

Use the `SELECT` statement to retrieve data from a table in Amazon DynamoDB\.

Using the `SELECT` statement can result in a full table scan if an equality condition with a partition key is not provided in the WHERE clause\. A scan operation examines every item for the requested values and can use up the provisioned throughput for a large table or index in a single operation\. 

If you want to avoid full table scan in PartiQL, you can:
+ Author your `SELECT` statements to not result in full table scans by making sure your [WHERE clause condition](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ql-reference.select.html#ql-reference.select.parameters) is configured accordingly\.
+ Disable full table scans using the IAM policy specified at [Example: Allow Select statements and deny full table scan statements in PartiQL for DynamoDB](ql-iam.md#access-policy-ql-iam-example6), in the DynamoDB developer guide\.

For more information see [Best Practices for Querying and Scanning Data](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-query-scan.html), in the DynamoDB developer guide\.

**Topics**
+ [Syntax](#ql-reference.select.syntax)
+ [Parameters](#ql-reference.select.parameters)
+ [Examples](#ql-reference.select.examples)

## Syntax<a name="ql-reference.select.syntax"></a>

```
SELECT expression  [, ...] 
FROM table[.index]
[ WHERE condition ] [ [ORDER BY key [DESC|ASC] , ...]
```

## Parameters<a name="ql-reference.select.parameters"></a>

***expression***  
\(Required\) A projection formed from the `*` wildcard or a projection list of one or more attribute names or document paths from the result set\. An expression can consist of calls to [Use PartiQL Functions with Amazon DynamoDB](ql-functions.md) or fields that are modified by [PartiQL Arithmetic, Comparison, and Logical Operators for DynamoDB ](ql-operators.md)\.

***table***  
\(Required\) The table name to query\.

***index***  
\(Optional\) The name of the index to query\.  
You must add double quotation marks to the table name and index name when querying an index\.  

```
SELECT * 
FROM "TableName"."IndexName"
```

***condition***  
\(Optional\) The selection criteria for the query\.  
To ensure that a `SELECT` statement does not result in a full table scan, the `WHERE` clause condition must specify a partition key\. Use the equality or IN operator\.  
For example, if you have an `Orders` table with an `OrderID` partition key and other non\-key attributes, including an `Address`, the following statements would not result in a full table scan:  

```
SELECT * 
FROM "Orders" 
WHERE OrderID = 100

SELECT * 
FROM "Orders" 
WHERE OrderID = 100 and Address='some address'

SELECT * 
FROM "Orders" 
WHERE OrderID = 100 or pk = 200

SELECT * 
FROM "Orders" 
WHERE OrderID IN [100, 300, 234]
```
The following `SELECT` statements, however, will result in a full table scan:  

```
SELECT * 
FROM "Orders" 
WHERE OrderID > 1

SELECT * 
FROM "Orders" 
WHERE Address='some address'

SELECT * 
FROM "Orders" 
WHERE OrderID = 100 OR Address='some address'
```

***key***  
\(Optional\) A hash key or a sort key to use to order returned results\. The default order is ascending \(`ASC`\) specify `DESC` if you want the results retuned in descending order\.

**Note**  
If you omit the `WHERE` clause, then all of the items in the table are retrieved\.

## Examples<a name="ql-reference.select.examples"></a>

The following query returns one item, if one exists, from the `Orders` table by specifying the partition key, `OrderID`, and using the equality operator\.

```
SELECT OrderID, Total
FROM "Orders"
WHERE OrderID = 1
```

The following query returns all items in the `Orders` table that have a specific partition key, `OrderID`, values using the OR operator\.

```
SELECT OrderID, Total
FROM "Orders"
WHERE OrderID = 1 OR OrderID = 2
```

The following query returns all items in the `Orders` table that have a specific partition key, `OrderID`, values using the IN operator\. The returned results are in descending order, based on the `OrderID` key attribute value\.

```
SELECT OrderID, Total
FROM "Orders"
WHERE OrderID IN [1, 2, 3] ORDER BY OrderID DESC
```

The following query shows a full table scan that returns all items from the `Orders` table that have a `Total` greater than 500, where `Total` is a non\-key attribute\.

```
SELECT OrderID, Total 
FROM "Orders"
WHERE Total > 500
```

The following query shows a full table scan that returns all items from the `Orders` table within a specific `Total` order range, using the IN operator and a non\-key attribute `Total`\.

```
SELECT OrderID, Total 
FROM "Orders"
WHERE Total IN [500, 600]
```

The following query shows a full table scan that returns all items from the `Orders` table within a specific `Total` order range, using the BETWEEN operator and a non\-key attribute `Total`\.

```
SELECT OrderID, Total 
FROM "Orders" 
WHERE Total BETWEEN 500 AND 600
```

The following query returns the first date a firestick device was used to watch by specifying the partition key `CustomerID` and sort key `MovieID` in the WHERE clause condition and using document paths in the SELECT clause\.

```
SELECT Devices.FireStick.DateWatched[0] 
FROM WatchList 
WHERE CustomerID= 'C1' AND MovieID= 'M1'
```

The following query shows a full table scan that returns the list of items where a firestick device was first used after 12/12/19 using document paths in the WHERE clause condition\.

```
SELECT Devices 
FROM WatchList 
WHERE Devices.FireStick.DateWatched[0] >= '12/12/19'
```