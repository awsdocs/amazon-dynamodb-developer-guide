# Best Practices for Querying and Scanning Data<a name="QueryAndScanGuidelines"></a>

This section covers some best practices for `Query` and `Scan` operations\.

## Performance Considerations for Scans<a name="QueryAndScanGuidelines.ScanPerformance"></a>

In general, `Scan` operations are less efficient than other operations in DynamoDB\.

A `Scan` operation always scans the entire table or secondary index, then filters out values to provide the desired result, essentially adding the extra step of removing data from the result set\. Avoid using a `Scan` operation on a large table or index with a filter that removes many results, if possible\. Also, as a table or index grows, the `Scan` operation slows\. The `Scan` operation examines every item for the requested values, and can use up the provisioned throughput for a large table or index in a single operation\. For faster response times, design your tables and indexes so that your applications can use `Query` instead of `Scan`\. \(For tables, you can also consider using the `GetItem` and `BatchGetItem` APIs\.\)\.

Alternatively, design your application to use `Scan` operations in a way that minimizes the impact on your request rate\.

## Avoid Sudden Spikes in Read Activity<a name="QueryAndScanGuidelines.SpikesOfActivity"></a>

When you create a table, you set its read and write capacity unit requirements\. For reads, the capacity units are expressed as the number of strongly consistent 4 KB data read requests per second\. For eventually consistent reads, a read capacity unit is two 4 KB read requests per second\. A `Scan` operation performs eventually consistent reads by default, and it can return up to 1 MB \(one page\) of data\. Therefore, a single `Scan` request can consume \(1 MB page size / 4 KB item size\) / 2 \(eventually consistent reads\) = 128 read operations\. If you were to request strongly consistent reads instead, the `Scan` operation would consume twice as much provisioned throughput—256 read operations\.

This represents a sudden spike in usage, compared to the configured read capacity for the table\. This usage of capacity units by a scan prevents other potentially more important requests for the same table from using the available capacity units\. As a result, you likely get a *ProvisionedThroughputExceeded* exception for those requests\.

Note that it is not just the sudden increase in capacity units the `Scan` uses that is a problem\. It is also because the scan is likely to consume all of its capacity units from the same partition because the scan requests read items that are next to each other on the partition\. This means that the request is hitting the same partition, causing all of its capacity units to be consumed, and throttling other requests to that partition\. If the request to read data had been spread across multiple partitions, then the operation would not have throttled a specific partition\. 

The following diagram illustrates the impact of a sudden spike of capacity unit usage by `Query` and `Scan` operations, and its impact on your other requests against the same table\. 

  

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

Instead of using a large `Scan` operation, you can use the following techniques to minimize the impact of a scan on a table's provisioned throughput\.

+ **Reduce Page Size**

  Because a Scan operation reads an entire page \(by default, 1 MB\), you can reduce the impact of the scan operation by setting a smaller page size\. The `Scan` operation provides a *Limit* parameter that you can use to set the page size for your request\. Each `Query` or `Scan` request that has a smaller page size uses fewer read operations and creates a "pause" between each request\. For example, if each item is 4 KB and you set the page size to 40 items, then a `Query` request would consume only 40 strongly consistent read operations or 20 eventually consistent read operations\. A larger number of smaller `Query` or `Scan` operations would allow your other critical requests to succeed without throttling\. 

+ **Isolate Scan Operations**

  DynamoDB is designed for easy scalability\. As a result, an application can create tables for distinct purposes, possibly even duplicating content across several tables\. You want to perform scans on a table that is not taking "mission\-critical" traffic\. Some applications handle this load by rotating traffic hourly between two tables – one for critical traffic, and one for bookkeeping\. Other applications can do this by performing every write on two tables: a "mission\-critical" table, and a "shadow" table\. 

You should configure your application to retry any request that receives a response code that indicates you have exceeded your provisioned throughput, or increase the provisioned throughput for your table using the `UpdateTable` operation\. If you have temporary spikes in your workload that cause your throughput to exceed, occasionally, beyond the provisioned level, retry the request with exponential backoff\. For more information about implementing exponential backoff, see [Error Retries and Exponential Backoff](Programming.Errors.md#Programming.Errors.RetryAndBackoff)\.

## Take Advantage of Parallel Scans<a name="QueryAndScanGuidelines.ParallelScan"></a>

Many applications can benefit from using parallel `Scan` operations rather than sequential scans\. For example, an application that processes a large table of historical data can perform a parallel scan much faster than a sequential one\. Multiple worker threads in a background "sweeper" process could scan a table at a low priority without affecting production traffic\. In each of these examples, a parallel `Scan` is used in such a way that it does not starve other applications of provisioned throughput resources\.

Although parallel scans can be beneficial, they can place a heavy demand on provisioned throughput\. With a parallel scan, your application will have multiple workers that are all running `Scan` operations concurrently, which can very quickly consume all of your table's provisioned read capacity\. In that case, other applications that need to access the table might be throttled\.

A parallel scan can be the right choice if the following conditions are met:

+ The table size is 20 GB or larger\.

+ The table's provisioned read throughput is not being fully utilized\.

+ Sequential `Scan` operations are too slow\.

### Choosing *TotalSegments*<a name="QueryAndScanGuidelines.ParallelScan.TotalSegments"></a>

The best setting for `TotalSegments` depends on your specific data, the table's provisioned throughput settings, and your performance requirements\. You will probably need to experiment to get it right\. We recommend that you begin with a simple ratio, such as one segment per 2 GB of data\. For example, for a 30 GB table, you could set `TotalSegments` to 15 \(30 GB / 2 GB\)\. Your application would then use fifteen workers, with each worker scanning a different segment\.

You can also choose a value for `TotalSegments` that is based on client resources\. You can set `TotalSegments` to any number from 1 to 1000000, and DynamoDB will allow you to scan that number of segments\. If, for example, your client limits the number of threads that can run concurrently, you can gradually increase `TotalSegments` until you get the best `Scan` performance with your application\.

You will need to monitor your parallel scans to optimize your provisioned throughput utilization, while also making sure that your other applications aren't starved of resources\. Increase the value for `TotalSegments` if you do not consume all of your provisioned throughput but still experience throttling in your `Scan` requests\. Reduce the value for `TotalSegments` if the `Scan` requests consume more provisioned throughput than you want to use\. 