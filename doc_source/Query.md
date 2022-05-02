# Working with Queries in DynamoDB<a name="Query"></a>

The `Query` operation in Amazon DynamoDB finds items based on primary key values\.

You must provide the name of the partition key attribute and a single value for that attribute\. `Query` returns all items with that partition key value\. Optionally, you can provide a sort key attribute and use a comparison operator to refine the search results\.

**Topics**
+ [Key Condition Expression](#Query.KeyConditionExpressions)
+ [Filter Expressions for Query](#Query.FilterExpression)
+ [Limiting the Number of Items in the Result Set](#Query.Limit)
+ [Paginating Table Query Results](Query.Pagination.md)
+ [Counting the Items in the Results](#Query.Count)
+ [Capacity Units Consumed by Query](#Query.CapacityUnits)
+ [Read Consistency for Query](#Query.ReadConsistency)
+ [Querying Tables and Indexes: Java](QueryingJavaDocumentAPI.md)
+ [Querying Tables and Indexes: \.NET](LowLevelDotNetQuerying.md)

## Key Condition Expression<a name="Query.KeyConditionExpressions"></a>

To specify the search criteria, you use a *key condition expression*—a string that determines the items to be read from the table or index\. 

You must specify the partition key name and value as an equality condition\. 

You can optionally provide a second condition for the sort key \(if present\)\. The sort key condition must use one of the following comparison operators:
+ `a = b` — true if the attribute *a* is equal to the value *b*
+ `a < b` — true if *a* is less than *b*
+ `a <= b` — true if *a* is less than or equal to *b*
+ `a > b` — true if *a* is greater than *b*
+ `a >= b` — true if *a* is greater than or equal to *b*
+ `a BETWEEN b AND c` — true if *a* is greater than or equal to *b*, and less than or equal to *c*\.

The following function is also supported:
+ `begins_with (a, substr)`— true if the value of attribute `a` begins with a particular substring\.

The following AWS Command Line Interface \(AWS CLI\) examples demonstrate the use of key condition expressions\. These expressions use placeholders \(such as `:name` and `:sub`\) instead of actual values\. For more information, see [Expression Attribute Names in DynamoDB](Expressions.ExpressionAttributeNames.md) and [Expression Attribute Values](Expressions.ExpressionAttributeValues.md)\.

**Example**  
Query the `Thread` table for a particular `ForumName` \(partition key\)\. All of the items with that `ForumName` value are read by the query because the sort key \(`Subject`\) is not included in `KeyConditionExpression`\.  

```
aws dynamodb query \
    --table-name Thread \
    --key-condition-expression "ForumName = :name" \
    --expression-attribute-values  '{":name":{"S":"Amazon DynamoDB"}}'
```

**Example**  
Query the `Thread` table for a particular `ForumName` \(partition key\), but this time return only the items with a given `Subject` \(sort key\)\.  

```
aws dynamodb query \
    --table-name Thread \
    --key-condition-expression "ForumName = :name and Subject = :sub" \
    --expression-attribute-values  file://values.json
```
The arguments for `--expression-attribute-values` are stored in the `values.json` file\.  

```
{
    ":name":{"S":"Amazon DynamoDB"},
    ":sub":{"S":"DynamoDB Thread 1"}
}
```

**Example**  
Query the `Reply` table for a particular `Id` \(partition key\), but return only those items whose `ReplyDateTime` \(sort key\) begins with certain characters\.  

```
aws dynamodb query \
    --table-name Reply \
    --key-condition-expression "Id = :id and begins_with(ReplyDateTime, :dt)" \
    --expression-attribute-values  file://values.json
```
The arguments for `--expression-attribute-values` are stored in the `values.json` file\.  

```
{
    ":id":{"S":"Amazon DynamoDB#DynamoDB Thread 1"},
    ":dt":{"S":"2015-09"}
}
```

You can use any attribute name in a key condition expression, provided that the first character is `a-z` or `A-Z` and the rest of the characters \(starting from the second character, if present\) are `a-z`, `A-Z`, or `0-9`\. In addition, the attribute name must not be a DynamoDB reserved word\. \(For a complete list of these, see [Reserved Words in DynamoDB](ReservedWords.md)\.\) If an attribute name does not meet these requirements, you must define an expression attribute name as a placeholder\. For more information, see [Expression Attribute Names in DynamoDB](Expressions.ExpressionAttributeNames.md)\.

For items with a given partition key value, DynamoDB stores these items close together, in sorted order by sort key value\. In a `Query` operation, DynamoDB retrieves the items in sorted order, and then processes the items using `KeyConditionExpression` and any `FilterExpression` that might be present\. Only then are the `Query` results sent back to the client\.

A `Query` operation always returns a result set\. If no matching items are found, the result set is empty\.

`Query` results are always sorted by the sort key value\. If the data type of the sort key is `Number`, the results are returned in numeric order\. Otherwise, the results are returned in order of UTF\-8 bytes\. By default, the sort order is ascending\. To reverse the order, set the `ScanIndexForward` parameter to `false`\.

A single `Query` operation can retrieve a maximum of 1 MB of data\. This limit applies before any `FilterExpression` is applied to the results\. If `LastEvaluatedKey` is present in the response and is non\-null, you must paginate the result set \(see [Paginating Table Query Results](Query.Pagination.md)\)\.

## Filter Expressions for Query<a name="Query.FilterExpression"></a>

If you need to further refine the `Query` results, you can optionally provide a filter expression\. A *filter expression* determines which items within the `Query` results should be returned to you\. All of the other results are discarded\.

A filter expression is applied after a `Query` finishes, but before the results are returned\. Therefore, a `Query` consumes the same amount of read capacity, regardless of whether a filter expression is present\.

A `Query` operation can retrieve a maximum of 1 MB of data\. This limit applies before the filter expression is evaluated\.

A filter expression cannot contain partition key or sort key attributes\. You need to specify those attributes in the key condition expression, not the filter expression\.

The syntax for a filter expression is similar to that of a key condition expression\. Filter expressions can use the same comparators, functions, and logical operators as a key condition expression\. In addition, filter expressions can use the not\-equals operator \(`<>`\), the `OR` operator, the `CONTAINS` operator, the `IN` operator, the `BEGINS_WITH` operator, the `BETWEEN` operator, the `EXISTS` operator, and the `SIZE` operator\. For more information, see [Key Condition Expression](#Query.KeyConditionExpressions) and [Syntax for Filter and Condition Expressions](Expressions.OperatorsAndFunctions.md#Expressions.OperatorsAndFunctions.Syntax)\.

**Example**  
The following AWS CLI example queries the `Thread` table for a particular `ForumName` \(partition key\) and `Subject` \(sort key\)\. Of the items that are found, only the most popular discussion threads are returned—in other words, only those threads with more than a certain number of `Views`\.  

```
aws dynamodb query \
    --table-name Thread \
    --key-condition-expression "ForumName = :fn and Subject = :sub" \
    --filter-expression "#v >= :num" \
    --expression-attribute-names '{"#v": "Views"}' \
    --expression-attribute-values file://values.json
```
The arguments for `--expression-attribute-values` are stored in the `values.json` file\.  

```
{
    ":fn":{"S":"Amazon DynamoDB"},
    ":sub":{"S":"DynamoDB Thread 1"},
    ":num":{"N":"3"}
}
```
Note that `Views` is a reserved word in DynamoDB \(see [Reserved Words in DynamoDB](ReservedWords.md)\), so this example uses `#v` as a placeholder\. For more information, see [Expression Attribute Names in DynamoDB](Expressions.ExpressionAttributeNames.md)\.

**Note**  
A filter expression removes items from the `Query` result set\. If possible, avoid using `Query` where you expect to retrieve a large number of items but also need to discard most of those items\.

## Limiting the Number of Items in the Result Set<a name="Query.Limit"></a>

The `Query` operation allows you to limit the number of items that it reads\. To do this, set the `Limit` parameter to the maximum number of items that you want\.

For example, suppose that you `Query` a table, with a `Limit` value of `6`, and without a filter expression\. The `Query` result contains the first six items from the table that match the key condition expression from the request\.

Now suppose that you add a filter expression to the `Query`\. In this case, DynamoDB reads up to six items, and then returns only those that match the filter expression\. The final `Query` result contains six items or fewer, even if more items would have matched the filter expression if DynamoDB had kept reading more items\.

## Counting the Items in the Results<a name="Query.Count"></a>

In addition to the items that match your criteria, the `Query` response contains the following elements:
+ `ScannedCount` — The number of items that matched the key condition expression *before* a filter expression \(if present\) was applied\.
+ `Count` — The number of items that remain *after* a filter expression \(if present\) was applied\.

**Note**  
If you don't use a filter expression, `ScannedCount` and `Count` have the same value\.

If the size of the `Query` result set is larger than 1 MB, `ScannedCount` and `Count` represent only a partial count of the total items\. You need to perform multiple `Query` operations to retrieve all the results \(see [Paginating Table Query Results](Query.Pagination.md)\)\.

Each `Query` response contains the `ScannedCount` and `Count` for the items that were processed by that particular `Query` request\. To obtain grand totals for all of the `Query` requests, you could keep a running tally of both `ScannedCount` and `Count`\.

## Capacity Units Consumed by Query<a name="Query.CapacityUnits"></a>

You can `Query` any table or secondary index, provided that it has a composite primary key \(partition key and sort key\)\. `Query` operations consume read capacity units, as follows\.


****  

| If you `Query` a\.\.\. | DynamoDB consumes read capacity units from\.\.\. | 
| --- | --- | 
| Table | The table's provisioned read capacity\. | 
| Global secondary index | The index's provisioned read capacity\. | 
| Local secondary index | The base table's provisioned read capacity\. | 

By default, a `Query` operation does not return any data on how much read capacity it consumes\. However, you can specify the `ReturnConsumedCapacity` parameter in a `Query` request to obtain this information\. The following are the valid settings for `ReturnConsumedCapacity`:
+ `NONE` — No consumed capacity data is returned\. \(This is the default\.\)
+ `TOTAL` — The response includes the aggregate number of read capacity units consumed\.
+ `INDEXES` — The response shows the aggregate number of read capacity units consumed, together with the consumed capacity for each table and index that was accessed\.

DynamoDB calculates the number of read capacity units consumed based on item size, not on the amount of data that is returned to an application\. For this reason, the number of capacity units consumed is the same whether you request all of the attributes \(the default behavior\) or just some of them \(using a projection expression\)\. The number is also the same whether or not you use a filter expression\.

## Read Consistency for Query<a name="Query.ReadConsistency"></a>

A `Query` operation performs eventually consistent reads, by default\. This means that the `Query` results might not reflect changes due to recently completed `PutItem` or `UpdateItem` operations\. For more information, see [Read Consistency](HowItWorks.ReadConsistency.md)\.

If you require strongly consistent reads, set the `ConsistentRead` parameter to `true` in the `Query` request\.