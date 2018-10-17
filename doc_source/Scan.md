# Working with Scans<a name="Scan"></a>

**Topics**
+ [Filter Expressions for *Scan*](#Scan.FilterExpression)
+ [Limiting the Number of Items in the Result Set](#Scan.Limit)
+ [Paginating the Results](#Scan.Pagination)
+ [Counting the Items in the Results](#Scan.Count)
+ [Capacity Units Consumed by *Scan*](#Scan.CapacityUnits)
+ [Read Consistency for *Scan*](#Scan.ReadConsistency)
+ [Parallel Scan](#Scan.ParallelScan)
+ [Scanning Tables and Indexes: Java](ScanJavaDocumentAPI.md)
+ [Scanning Tables and Indexes: \.NET](LowLevelDotNetScanning.md)

A `Scan` operation reads every item in a table or a secondary index\. By default, a `Scan` operation returns all of the data attributes for every item in the table or index\. You can use the `ProjectionExpression` parameter so that `Scan` only returns some of the attributes, rather than all of them\.

`Scan` always returns a result set\. If no matching items are found, the result set will be empty\.

A single `Scan` request can retrieve a maximum of 1 MB of data; DynamoDB can optionally apply a filter expression to this data, narrowing the results before they are returned to the user\.

## Filter Expressions for *Scan*<a name="Scan.FilterExpression"></a>

If you need to further refine the `Scan` results, you can optionally provide a filter expression\. A *filter expression* determines which items within the `Scan` results should be returned to you\. All of the other results are discarded\.

A filter expression is applied after a `Scan` finishes, but before the results are returned\. Therefore, a `Scan` will consume the same amount of read capacity, regardless of whether a filter expression is present\.

A `Scan` operation can retrieve a maximum of 1 MB of data\. This limit applies before the filter expression is evaluated\.

With `Scan`, you can specify any attributes in a filter expression—including partition key and sort key attributes\.

The syntax for a filter expression is identical to that of a condition expression\. Filter expressions can use the same comparators, functions, and logical operators as a condition expression\. For more information, [Condition Expressions](Expressions.ConditionExpressions.md)\.

**Example**  
The following AWS CLI example scans the *Thread* table and returns only the items that were last posted to by a particular user\.  

```
aws dynamodb scan \
     --table-name Thread \
     --filter-expression "LastPostedBy = :name" \
     --expression-attribute-values '{":name":{"S":"User A"}}'
```

## Limiting the Number of Items in the Result Set<a name="Scan.Limit"></a>

The `Scan` operation allows you to limit the number of items that it returns in the result\. To do this, set the `Limit` parameter to the maximum number of items that you want\.

For example, suppose you `Scan` a table, with a `Limit` value of `6`, and without a filter expression\. The `Scan` result will contain the first six items from the table that match the key condition expression from the request\.

Now suppose you add a filter expression to the `Scan`\. In this case, DynamoDB will apply the filter expression to the six items that were returned, discarding those that do not match\. The final `Scan` result will contain 6 items or fewer, depending on the number of items that were filtered\.

## Paginating the Results<a name="Scan.Pagination"></a>

DynamoDB *paginates* the results from `Scan` operations\. With pagination, the `Scan` results are divided into "pages" of data that are 1 MB in size \(or less\)\. An application can process the first page of results, then the second page, and so on\.

A single `Scan` will only return a result set that fits within the 1 MB size limit\. To determine whether there are more results, and to retrieve them one page at a time, applications should do the following:

1. Examine the low\-level `Scan` result:
   + If the result contains a `LastEvaluatedKey` element, proceed to step 2\.
   + If there is *not* a `LastEvaluatedKey` in the result, then there are no more items to be retrieved\.

1. Construct a new `Scan` request, with the same parameters as the previous one—but this time, take the `LastEvaluatedKey` value from step 1 and use it as the `ExclusiveStartKey` parameter in the new `Scan` request\.

1. Run the new `Scan` request\.

1. Go to step 1\.

In other words, the `LastEvaluatedKey` from a `Scan` response should be used as the `ExclusiveStartKey` for the next `Scan` request\. If there is not a `LastEvaluatedKey` element in a `Scan` response, then you have retrieved the final page of results\. \(The absence of `LastEvaluatedKey` is the only way to know that you have reached the end of the result set\.\)

You can use the AWS CLI to view this behavior\. The CLI sends low\-level `Scan` requests to DynamoDB, repeatedly, until `LastEvaluatedKey` is no longer present in the results\. Consider the following AWS CLI example that scans the entire *Movies* table but returns only the movies from a particular genre:

```
aws dynamodb scan \
    --table-name Movies \
    --projection-expression "title" \
    --filter-expression 'contains(info.genres,:gen)' \
    --expression-attribute-values '{":gen":{"S":"Sci-Fi"}}' \
    --page-size 100  \
    --debug
```

Ordinarily, the AWS CLI handles pagination automatically; however, in this example, the CLI's `--page-size` parameter limits the number of items per page\. The `--debug` parameter prints low\-level information about requests and responses\.

If you run the example, the first response from DynamoDB looks similar to this:

```
2017-07-07 12:19:14,389 - MainThread - botocore.parsers - DEBUG - Response body:
b'{"Count":7,"Items":[{"title":{"S":"Monster on the Campus"}},{"title":{"S":"+1"}},
{"title":{"S":"100 Degrees Below Zero"}},{"title":{"S":"About Time"}},{"title":{"S":"After Earth"}},
{"title":{"S":"Age of Dinosaurs"}},{"title":{"S":"Cloudy with a Chance of Meatballs 2"}}],
"LastEvaluatedKey":{"year":{"N":"2013"},"title":{"S":"Curse of Chucky"}},"ScannedCount":100}'
```

The `LastEvaluatedKey` in the response indicates that not all of the items have been retrieved\. The AWS CLI will then issue another `Scan` request to DynamoDB\. This request and response pattern continues, until the final response:

```
2017-07-07 12:19:17,830 - MainThread - botocore.parsers - DEBUG - Response body:
b'{"Count":1,"Items":[{"title":{"S":"WarGames"}}],"ScannedCount":6}'
```

The absence of `LastEvaluatedKey` indicates that there are no more items to retrieve\.

**Note**  
The AWS SDKs handle the low\-level DynamoDB responses \(including the presence or absence of `LastEvaluatedKey`\), and provide various abstractions for paginating `Scan` results\. For example, the SDK for Java document interface provides `java.util.Iterator` support, so that you can walk through the results one at a time\.  
For code samples in various programming languages, see the [Amazon DynamoDB Getting Started Guide](http://docs.aws.amazon.com/amazondynamodb/latest/gettingstartedguide/) and the AWS SDK documentation for your language\.

## Counting the Items in the Results<a name="Scan.Count"></a>

In addition to the items that match your criteria, the `Scan` response contains the following elements:
+ `ScannedCount` — the number of items that matched the key condition expression, *before* a filter expression \(if present\) was applied\.
+ `Count` — the number of items that remain, *after* a filter expression \(if present\) was applied\.

**Note**  
If you do not use a filter expression, then `ScannedCount` and `Count` will have the same value\.

If the size of the `Scan` result set is larger than 1 MB, then `ScannedCount` and `Count` will represent only a partial count of the total items\. You will need to perform multiple `Scan` operations in order to retrieve all of the results \(see [Paginating the Results](#Scan.Pagination)\)\.

Each `Scan` response will contain the `ScannedCount` and `Count` for the items that were processed by that particular `Scan` request\. To obtain grand totals for all of the `Scan` requests, you could keep a running tally of both `ScannedCount` and `Count`\.

## Capacity Units Consumed by *Scan*<a name="Scan.CapacityUnits"></a>

You can `Scan` any table or secondary index\. `Scan` operations consume read capacity units, as follows:


****  

| If you `Scan` a\.\.\. | DynamoDB consumes read capacity units from\.\.\. | 
| --- | --- | 
| Table | The table's provisioned read capacity\. | 
| Global secondary index | The index's provisioned read capacity\. | 
| Local secondary index | The base table's provisioned read capacity\. | 

By default, a `Scan` operation does not return any data on how much read capacity it consumes\. However, you can specify the `ReturnConsumedCapacity` parameter in a `Scan` request to obtain this information\. The following are the valid settings for `ReturnConsumedCapacity`:
+ `NONE`—no consumed capacity data is returned\. \(This is the default\.\)
+ `TOTAL`—the response includes the aggregate number of read capacity units consumed\.
+ `INDEXES`—the response shows the aggregate number of read capacity units consumed, together with the consumed capacity for each table and index that was accessed\.

DynamoDB calculates the number of read capacity units consumed based on item size, not on the amount of data that is returned to an application\. For this reason, the number of capacity units consumed will be the same whether you request all of the attributes \(the default behavior\) or just some of them \(using a projection expression\)\. The number will also be the same whether or not you use a filter expression\.

## Read Consistency for *Scan*<a name="Scan.ReadConsistency"></a>

A `Scan` operation performs eventually consistent reads, by default\. This means that the `Scan` results might not reflect changes due to recently completed `PutItem` or `UpdateItem` operations\. For more information, see [Read Consistency](HowItWorks.ReadConsistency.md)\.

If you require strongly consistent reads, as of the time that the `Scan` begins, set the `ConsistentRead` parameter to `true` in the `Scan` request\. This will ensure that all of the write operations that completed before the `Scan` began will be included in the `Scan` response\. 

Setting `ConsistentRead` to `true` can be useful in table backup or replication scenarios, in conjunction with [DynamoDB Streams](./Streams.html): You first use `Scan` with `ConsistentRead` set to true, in order to obtain a consistent copy of the data in the table\. During the `Scan`, DynamoDB Streams records any additional write activity that occurs on the table\. After the `Scan` completes, you can apply the write activity from the stream to the table\.

**Note**  
Note that a `Scan` operation with `ConsistentRead` set to `true` will consume twice as many read capacity units, as compared to leaving `ConsistentRead` at its default value \(`false`\)\.

## Parallel Scan<a name="Scan.ParallelScan"></a>

By default, the `Scan` operation processes data sequentially\. DynamoDB returns data to the application in 1 MB increments, and an application performs additional `Scan` operations to retrieve the next 1 MB of data\. 

The larger the table or index being scanned, the more time the `Scan` will take to complete\. In addition, a sequential `Scan` might not always be able to fully utilize the provisioned read throughput capacity: Even though DynamoDB distributes a large table's data across multiple physical partitions, a `Scan` operation can only read one partition at a time\. For this reason, the throughput of a `Scan` is constrained by the maximum throughput of a single partition\.

To address these issues, the `Scan` operation can logically divide a table or secondary index into multiple *segments*, with multiple application workers scanning the segments in parallel\. Each worker can be a thread \(in programming languages that support multithreading\) or an operating system process\. To perform a parallel scan, each worker issues its own `Scan` request with the following parameters:
+ `Segment —` A segment to be scanned by a particular worker\. Each worker should use a different value for `Segment`\.
+ `TotalSegments —` The total number of segments for the parallel scan\. This value must be the same as the number of workers that your application will use\.

The following diagram shows how a multithreaded application performs a parallel `Scan` with three degrees of parallelism:

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/ParallelScan.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

In this diagram, the application spawns three threads and assigns each thread a number\. \(Segments are zero\-based, so the first number is always 0\.\) Each thread issues a `Scan` request, setting `Segment` to its designated number and setting `TotalSegments` to 3\. Each thread scans its designated segment, retrieving data 1 MB at a time, and returns the data to the application's main thread\.

The values for `Segment` and `TotalSegments` apply to individual `Scan` requests, and you can use different values at any time\. You might need to experiment with these values, and the number of workers you use, until your application achieves its best performance\.

**Note**  
A parallel scan with a large number of workers can easily consume all of the provisioned throughput for the table or index being scanned\. It is best to avoid such scans if the table or index is also incurring heavy read or write activity from other applications\.

To control the amount of data returned per request, use the `Limit` parameter\. This can help prevent situations where one worker consumes all of the provisioned throughput, at the expense of all other workers\.

## Parallel Scan Performance<a name="Scan.ParallelScanPerformance"></a>

Parallel scan segments map very closely to the underlying storage for DynamoDB tables\. This means that if many clients scan many sequential segments in parallel with a very large number of total segments, the segments all map onto keys that are stored on the same underlying storage\. This can cause throttling and an inability to take advantage of all provisioned throughput, as all data is mapping to a single portion of the DynamoDB service\.

An example of this is scanning segments 0, 1, 2, 3, 4 and 5 of 1,000,000 total segments simultaniously.

Instead, split the segment space into equal portions per thread - for example with 2 threads, split the total segment space into ranges from 0 to 499999 and 500000 to 999999 and scan those spaces individually, so segments 0 and 500000 are scanned first, then 1 and 500001, and so on\.
