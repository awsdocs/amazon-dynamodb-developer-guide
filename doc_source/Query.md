# Working with Queries<a name="Query"></a>


+ [Key Condition Expression](#Query.KeyConditionExpressions)
+ [Filter Expressions for *Query*](#Query.FilterExpression)
+ [Limiting the Number of Items in the Result Set](#Query.Limit)
+ [Paginating the Results](#Query.Pagination)
+ [Counting the Items in the Results](#Query.Count)
+ [Capacity Units Consumed by *Query*](#Query.CapacityUnits)
+ [Read Consistency for *Query*](#Query.ReadConsistency)
+ [Querying Tables and Indexes: Java](QueryingJavaDocumentAPI.md)
+ [Querying Tables and Indexes: \.NET](LowLevelDotNetQuerying.md)

The `Query` operation finds items based on primary key values\. You can query any table or secondary index that has a composite primary key \(a partition key and a sort key\)\.

You must provide the name of the partition key attribute, and a single value for that attribute\. `Query` will return all of the items with that partition key value\. You can optionally provide a sort key attribute, and use a comparison operator to refine the search results\.

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

The following AWS CLI examples demonstrate the use of key condition expressions\. Note that these expressions use placeholders \(such as `:name` and `:sub`\) instead of actual values\. For more information, see [Expression Attribute Names](Expressions.ExpressionAttributeNames.md) and [Expression Attribute Values](Expressions.ExpressionAttributeValues.md)\.

**Example**  
Query the *Thread* table for a particular *ForumName* \(partition key\)\. All of the items with that *ForumName* value will be read by the query, because the sort key \(*Subject*\) is not included in `KeyConditionExpression`\.  

```
aws dynamodb query \
    --table-name Thread \
    --key-condition-expression "ForumName = :name" \
    --expression-attribute-values  '{":name":{"S":"Amazon DynamoDB"}}'
```

**Example**  
Query the *Thread* table for a particular *ForumName* \(partition key\), but this time return only the items with a given *Subject* \(sort key\)\.  

```
aws dynamodb query \
    --table-name Thread \
    --key-condition-expression "ForumName = :name and Subject = :sub" \
    --expression-attribute-values  file://values.json
```
The arguments for `--expression-attribute-values` are stored in the file `values.json`:  

```
{
    ":name":{"S":"Amazon DynamoDB"},
    ":sub":{"S":"DynamoDB Thread 1"}
}
```

**Example**  
Query the *Reply* table for a particular *Id* \(partition key\), but return only those items whose *ReplyDateTime* \(sort key\) begins with certain characters\.  

```
aws dynamodb query \
    --table-name Reply \
    --key-condition-expression "Id = :id and begins_with(ReplyDateTime, :dt)" \
    --expression-attribute-values  file://values.json
```
The arguments for `--expression-attribute-values` are stored in the file `values.json`:  

```
{
    ":id":{"S":"Amazon DynamoDB#DynamoDB Thread 1"},
    ":dt":{"S":"2015-09"}
}
```

You can use any attribute name in a key condition expression, provided that the first character is `a-z` or `A-Z` and the second character \(if present\) is `a-z`, `A-Z`, or `0-9`\. In addition, the attribute name must not be a DynamoDB reserved word\. \(For a complete list of these, see [Reserved Words in DynamoDB](ReservedWords.md)\.\) If an attribute name does not meet these requirements, you will need to define an expression attribute name as a placeholder\. For more information, see [Expression Attribute Names](Expressions.ExpressionAttributeNames.md)\.

For items with a given partition key value, DynamoDB stores these items close together, in sorted order by sort key value\. In a `Query` operation, DynamoDB retrieves the items in sorted order, and then processes the items using `KeyConditionExpression` and any `FilterExpression` that might be present\. Only then are the `Query` results sent back to the client\.

A `Query` operation always returns a result set\. If no matching items are found, the result set will be empty\.

`Query` results are always sorted by the sort key value\. If the data type of the sort key is Number, the results are returned in numeric order; otherwise, the results are returned in order of UTF\-8 bytes\. By default, the sort order is ascending\. To reverse the order, set the `ScanIndexForward` parameter to `false`\.

A single `Query` operation can retrieve a maximum of 1 MB of data\. This limit applies before any `FilterExpression` is applied to the results\. If `LastEvaluatedKey` is present in the response and is non\-null, you will need to paginate the result set \(see [Paginating the Results](#Query.Pagination)\)\.

## Filter Expressions for *Query*<a name="Query.FilterExpression"></a>

If you need to further refine the `Query` results, you can optionally provide a filter expression\. A *filter expression* determines which items within the `Query` results should be returned to you\. All of the other results are discarded\.

A filter expression is applied after a `Query` finishes, but before the results are returned\. Therefore, a `Query` will consume the same amount of read capacity, regardless of whether a filter expression is present\.

A `Query` operation can retrieve a maximum of 1 MB of data\. This limit applies before the filter expression is evaluated\.

A filter expression cannot contain partition key or sort key attributes\. You need to specify those attributes in the key condition expression, not the filter expression\.

The syntax for a filter expression is identical to that of a condition expression\. Filter expressions can use the same comparators, functions, and logical operators as a condition expression\. For more information, [Condition Expressions](Expressions.ConditionExpressions.md)\.

**Example**  
The following AWS CLI example queries the *Thread* table for a particular *ForumName* \(partition key\) and *Subject* \(sort key\)\. Of the items that are found, only the most popular discussion threads are returned—in other words, only those threads with more than a certain number of *Views*\.  

```
aws dynamodb query \
    --table-name Thread \
    --key-condition-expression "ForumName = :fn" \
    --filter-expression "#v >= :num" \
    --expression-attribute-names '{"#v": "Views"}' \
    --expression-attribute-values file://values.json
```
The arguments for `--expression-attribute-values` are stored in the file `values.json`:  

```
{
    ":fn":{"S":"Amazon DynamoDB#DynamoDB Thread 1"},
    ":num":{"N":"3"}
}
```
Note that `Views` is a reserved word in DynamoDB \(see [Reserved Words in DynamoDB](ReservedWords.md)\), so this example uses `#v` as a placeholder\. For more information, see [Expression Attribute Names](Expressions.ExpressionAttributeNames.md)\.

**Note**  
A filter expression removes items from the `Query` result set\. If possible, avoid using `Query` where you expect to retrieve a large number of items, but also need to discard most of those items\.

## Limiting the Number of Items in the Result Set<a name="Query.Limit"></a>

The `Query` operation allows you to limit the number of items that it returns in the result\. To do this, set the `Limit` parameter to the maximum number of items that you want\.

For example, suppose you `Query` a table, with a `Limit` value of `6`, and without a filter expression\. The `Query` result will contain the first six items from the table that match the key condition expression from the request\.

Now suppose you add a filter expression to the `Query`\. In this case, DynamoDB will apply the filter expression to the six items that were returned, discarding those that do not match\. The final `Query` result will contain 6 items or fewer, depending on the number of items that were filtered\.

## Paginating the Results<a name="Query.Pagination"></a>

DynamoDB *paginates* the results from `Query` operations\. With pagination, the `Query` results are divided into "pages" of data that are 1 MB in size \(or less\)\. An application can process the first page of results, then the second page, and so on\.

A single `Query` will only return a result set that fits within the 1 MB size limit\. To determine whether there are more results, and to retrieve them one page at a time, applications should do the following: 

1. Examine the low\-level `Query` result:

   + If the result contains a `LastEvaluatedKey` element, proceed to step 2\.

   + If there is *not* a `LastEvaluatedKey` in the result, then there are no more items to be retrieved\.

1. Construct a new `Query` request, with the same parameters as the previous one—but this time, take the `LastEvaluatedKey` value from step 1 and use it as the `ExclusiveStartKey` parameter in the new `Query` request\.

1. Run the new `Query` request\.

1. Go to step 1\.

In other words, the `LastEvaluatedKey` from a `Query` response should be used as the `ExclusiveStartKey` for the next `Query` request\. If there is not a `LastEvaluatedKey` element in a `Query` response, then you have retrieved the final page of results\. \(The absence of `LastEvaluatedKey` is the only way to know that you have reached the end of the result set\.\)

You can use the AWS CLI to view this behavior\. The CLI sends low\-level `Query` requests to DynamoDB, repeatedly, until `LastEvaluatedKey` is no longer present in the results\. Consider the following AWS CLI example that retrieves movie titles from a particular year:

```
aws dynamodb query --table-name Movies \
    --projection-expression "title" \
    --key-condition-expression "#y = :yyyy" \
    --expression-attribute-names '{"#y":"year"}' \
    --expression-attribute-values '{":yyyy":{"N":"1993"}}' \
    --page-size 5 \
    --debug
```

Ordinarily, the AWS CLI handles pagination automatically; however, in this example, the CLI's `--page-size` parameter limits the number of items per page\. The `--debug` parameter prints low\-level information about requests and responses\.

If you run the example, the first response from DynamoDB looks similar to this:

```
2017-07-07 11:13:15,603 - MainThread - botocore.parsers - DEBUG - Response body:
b'{"Count":5,"Items":[{"title":{"S":"A Bronx Tale"}},
{"title":{"S":"A Perfect World"}},{"title":{"S":"Addams Family Values"}},
{"title":{"S":"Alive"}},{"title":{"S":"Benny & Joon"}}],
"LastEvaluatedKey":{"year":{"N":"1993"},"title":{"S":"Benny & Joon"}},
"ScannedCount":5}'
```

The `LastEvaluatedKey` in the response indicates that not all of the items have been retrieved\. The AWS CLI will then issue another `Query` request to DynamoDB\. This request and response pattern continues, until the final response:

```
2017-07-07 11:13:16,291 - MainThread - botocore.parsers - DEBUG - Response body:
b'{"Count":1,"Items":[{"title":{"S":"What\'s Eating Gilbert Grape"}}],"ScannedCount":1}'
```

The absence of `LastEvaluatedKey` indicates that there are no more items to retrieve\.

**Note**  
The AWS SDKs handle the low\-level DynamoDB responses \(including the presence or absence of `LastEvaluatedKey`\), and provide various abstractions for paginating `Query` results\. For example, the SDK for Java document interface provides `java.util.Iterator` support, so that you can walk through the results one at a time\.  
For code samples in various programming languages, see the [Amazon DynamoDB Getting Started Guide](http://docs.aws.amazon.com/amazondynamodb/latest/gettingstartedguide/) and the AWS SDK documentation for your language\.

## Counting the Items in the Results<a name="Query.Count"></a>

In addition to the items that match your criteria, the `Query` response contains the following elements:

+ `ScannedCount` — the number of items that matched the key condition expression, *before* a filter expression \(if present\) was applied\.

+ `Count` — the number of items that remain, *after* a filter expression \(if present\) was applied\.

**Note**  
If you do not use a filter expression, then `ScannedCount` and `Count` will have the same value\.

If the size of the `Query` result set is larger than 1 MB, then `ScannedCount` and `Count` will represent only a partial count of the total items\. You will need to perform multiple `Query` operations in order to retrieve all of the results \(see [Paginating the Results](#Query.Pagination)\)\.

Each `Query` response will contain the `ScannedCount` and `Count` for the items that were processed by that particular `Query` request\. To obtain grand totals for all of the `Query` requests, you could keep a running tally of both `ScannedCount` and `Count`\.

## Capacity Units Consumed by *Query*<a name="Query.CapacityUnits"></a>

You can `Query` any table or secondary index, provided that it has a composite primary key \(partition key and sort key\)\. `Query` operations consume read capacity units, as follows:


****  

| If you `Query` a\.\.\. | DynamoDB consumes read capacity units from\.\.\. | 
| --- | --- | 
| Table | The table's provisioned read capacity\. | 
| Global secondary index | The index's provisioned read capacity\. | 
| Local secondary index | The base table's provisioned read capacity\. | 

By default, a `Query` operation does not return any data on how much read capacity it consumes\. However, you can specify the `ReturnConsumedCapacity` parameter in a `Query` request to obtain this information\. The following are the valid settings for `ReturnConsumedCapacity`:

+ `NONE`—no consumed capacity data is returned\. \(This is the default\.\)

+ `TOTAL`—the response includes the aggregate number of read capacity units consumed\.

+ `INDEXES`—the response shows the aggregate number of read capacity units consumed, together with the consumed capacity for each table and index that was accessed\.

DynamoDB calculates the number of read capacity units consumed based on item size, not on the amount of data that is returned to an application\. For this reason, the number of capacity units consumed will be the same whether you request all of the attributes \(the default behavior\) or just some of them \(using a projection expression\)\. The number will also be the same whether or not you use a filter expression\.

## Read Consistency for *Query*<a name="Query.ReadConsistency"></a>

A `Query` operation performs eventually consistent reads, by default\. This means that the `Query` results might not reflect changes due to recently completed `PutItem` or `UpdateItem` operations\. For more information, see [Read Consistency](HowItWorks.ReadConsistency.md)\.

If you require strongly consistent reads, set the `ConsistentRead` parameter to *true* in the `Query` request\.
