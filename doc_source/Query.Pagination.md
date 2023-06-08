# Paginating table query results<a name="Query.Pagination"></a>

DynamoDB *paginates* the results from `Query` operations\. With pagination, the `Query` results are divided into "pages" of data that are 1 MB in size \(or less\)\. An application can process the first page of results, then the second page, and so on\.

A single `Query` only returns a result set that fits within the 1 MB size limit\. To determine whether there are more results, and to retrieve them one page at a time, applications should do the following: 

1. Examine the low\-level `Query` result:
   + If the result contains a `LastEvaluatedKey` element and it's non\-null, proceed to step 2\.
   + If there is *not* a `LastEvaluatedKey` in the result, there are no more items to be retrieved\.

1. Construct a new `Query` request, with the same parameters as the previous one\. However, this time, take the `LastEvaluatedKey` value from step 1 and use it as the `ExclusiveStartKey` parameter in the new `Query` request\.

1. Run the new `Query` request\.

1. Go to step 1\.

In other words, the `LastEvaluatedKey` from a `Query` response should be used as the `ExclusiveStartKey` for the next `Query` request\. If there is not a `LastEvaluatedKey` element in a `Query` response, then you have retrieved the final page of results\. If `LastEvaluatedKey` is not empty, it does not necessarily mean that there is more data in the result set\. The only way to know when you have reached the end of the result set is when `LastEvaluatedKey` is empty\.

You can use the AWS CLI to view this behavior\. The AWS CLI sends low\-level `Query` requests to DynamoDB repeatedly, until `LastEvaluatedKey` is no longer present in the results\. Consider the following AWS CLI example that retrieves movie titles from a particular year\.

```
aws dynamodb query --table-name Movies \
    --projection-expression "title" \
    --key-condition-expression "#y = :yyyy" \
    --expression-attribute-names '{"#y":"year"}' \
    --expression-attribute-values '{":yyyy":{"N":"1993"}}' \
    --page-size 5 \
    --debug
```

Ordinarily, the AWS CLI handles pagination automatically\. However, in this example, the AWS CLI `--page-size` parameter limits the number of items per page\. The `--debug` parameter prints low\-level information about requests and responses\.

If you run the example, the first response from DynamoDB looks similar to the following\.

```
2017-07-07 11:13:15,603 - MainThread - botocore.parsers - DEBUG - Response body:
b'{"Count":5,"Items":[{"title":{"S":"A Bronx Tale"}},
{"title":{"S":"A Perfect World"}},{"title":{"S":"Addams Family Values"}},
{"title":{"S":"Alive"}},{"title":{"S":"Benny & Joon"}}],
"LastEvaluatedKey":{"year":{"N":"1993"},"title":{"S":"Benny & Joon"}},
"ScannedCount":5}'
```

The `LastEvaluatedKey` in the response indicates that not all of the items have been retrieved\. The AWS CLI then issues another `Query` request to DynamoDB\. This request and response pattern continues, until the final response\.

```
2017-07-07 11:13:16,291 - MainThread - botocore.parsers - DEBUG - Response body:
b'{"Count":1,"Items":[{"title":{"S":"What\'s Eating Gilbert Grape"}}],"ScannedCount":1}'
```

The absence of `LastEvaluatedKey` indicates that there are no more items to retrieve\.

**Note**  
The AWS SDKs handle the low\-level DynamoDB responses \(including the presence or absence of `LastEvaluatedKey`\) and provide various abstractions for paginating `Query` results\. For example, the SDK for Java document interface provides `java.util.Iterator` support so that you can walk through the results one at a time\.  
For code examples in various programming languages, see the [Amazon DynamoDB Getting Started Guide](https://docs.aws.amazon.com/amazondynamodb/latest/gettingstartedguide/) and the AWS SDK documentation for your language\.

You can also reduce page size by limiting the number of items in the result set, with the `Limit` parameter of the `Query` operation\.

For more information about querying with DynamoDB, see [Query operations in DynamoDB](Query.md)\.