# Evaluate your table usage patterns<a name="CostOptimization_TableUsagePatterns"></a>

This section provides an overview of how to evaluate if you are efficiently using your DynamoDB tables\. There are certain usage patterns which are not optimal for DynamoDB, and they allow room for optimization from both a performance and cost perspective\.

**Topics**
+ [Perform fewer strongly\-consistent read operations](#CostOptimization_TableUsagePatterns_StronglyConsistentReads)
+ [Perform fewer transactions for read operations](#CostOptimization_TableUsagePatterns_Transactions)
+ [Perform fewer scans](#CostOptimization_TableUsagePatterns_Scans)
+ [Shorten attribute names](#CostOptimization_TableUsagePatterns_AttributeNames)
+ [Enable Time to Live \(TTL\)](#CostOptimization_TableUsagePatterns_TTL)
+ [Replace global tables with cross\-Region backups](#CostOptimization_TableUsagePatterns_GlobalTables)

## Perform fewer strongly\-consistent read operations<a name="CostOptimization_TableUsagePatterns_StronglyConsistentReads"></a>

DynamoDB allows you to configure [read consistency](HowItWorks.ReadConsistency.md) on a per\-request basis\. Read requests are eventually consistent by default\. Eventually consistent reads are charged at 0\.5 RCU for upto 4 KB of data\.

Most parts of distributed workloads are flexible and can tolerate eventual consistency\. However, there can be access patterns requiring strongly consistent reads\. Strongly consistent reads are charged at 1 RCU for upto 4 KB of data, essentially doubling your read costs\. DynamoDB provides you with the flexibility to use both consistency models on the same table\. 

You can evaluate your workload and application code to confirm if strongly consistent reads are used only where required\.

## Perform fewer transactions for read operations<a name="CostOptimization_TableUsagePatterns_Transactions"></a>

DynamoDB allows you to group certain actions in an all\-or\-nothing manner, which means you have the ability to execute ACID transactions with DynamoDB\. However, as is the case with relational databases, it is not necessary to follow this approach for every action\.

A [transactional read operation](transaction-apis.md#transaction-capacity-handling.title) of up to 4 KB consumes 2 RCUs as opposed to the default 0\.5 RCUs for reading the same amount of data in an eventually consistent manner\. The costs are doubled in write operations which means, a transactional write of up to 1 KB equates to 2 WCUs\.

To determine if all operations on your tables are transactions, CloudWatch metrics for the table can be filtered down to the transaction APIs\. If transaction APIs are the only graphs available under the `SuccessfulRequestLatency` metrics for the table, this would confirm that every operation is a transaction for this table\. Alternatively, if the overall capacity utilization trend matches the transaction API trend, consider revisiting the application design as it seems dominated by transactional APIs\.

## Perform fewer scans<a name="CostOptimization_TableUsagePatterns_Scans"></a>

The extensive use of `Scan` operations generally stems from the need to run analytical queries on the DynamoDB data\. Running frequent `Scan` operations on large table can be inefficient and expensive\.

A better alternative is to use the [Export to S3](S3DataExport.HowItWorks.md#S3DataExport.HowItWorks.title) feature and choosing a point in time to export the table state to S3\. AWS offers services like Athena which can then be used to run analytical queries on the data without consuming any capacity from the table\.

The frequency for `Scan` operations can be determined using the `SampleCount` statistic under the `SuccessfulRequestLatency` metric for `Scan`\. If `Scan` operations are indeed very frequent, the access patterns and data model should be re\-evaluated\.

## Shorten attribute names<a name="CostOptimization_TableUsagePatterns_AttributeNames"></a>

The total size of an item in DynamoDB is the sum of its attribute name lengths and values\. Having long attribute names not only contributes towards storage costs, but it might also lead to higher RCU/WCU consumption\. We recommend that you choose shorter attribute names rather than long ones\. Having shorter attribute names can help limit the item size within the next 4KB/1KB boundary after which you would consume additional RCU/WCU to access data\.

## Enable Time to Live \(TTL\)<a name="CostOptimization_TableUsagePatterns_TTL"></a>

[Time to Live \(TTL\)](TTL.md#TTL.title) can identify items older than the expiry time that you have set on an item and remove them from the table\. If your data grows over time and older data becomes irrelevant, enabling TTL on the table can help trim your data down and save on storage costs\.

 Another useful aspect of TTL is that the expired items occur on your DynamoDB streams, so rather than just removing the data from your data, it is possible to consume those items from the stream and archive them to a lower cost storage tier\. Additionally, deleting items via TTL comes at no additional cost — it does not consume capacity, and there’s no overhead of designing a clean up application\.

## Replace global tables with cross\-Region backups<a name="CostOptimization_TableUsagePatterns_GlobalTables"></a>

[Global tables](GlobalTables.md#GlobalTables.title) allow you to maintain multiple active replica tables in different Regions — they can all accept write operations and replicate data across each other\. It is easy to set up replicas and the synchronization is managed for you\. The replicas converge to a consistent state using a last writer wins strategy\.

If you are using Global tables purely as a part of failover or disaster recovery \(DR\) strategy, you can replace them with a cross\-Region backup copy for relatively lenient recovery point objectives and recovery time objective requirements\. If you do not require fast local access and the high availability of five nines, maintaining a global table replica might not be the best approach for failover\.

As an alternative, consider using AWS Backup to manage DynamoDB backups\. You can schedule regular backups and copy them across Regions to meet DR requirements in a more cost\-effective approach compared to using Global tables\.