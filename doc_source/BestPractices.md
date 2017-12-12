# Best Practices for DynamoDB<a name="BestPractices"></a>

Use this section to quickly find recommendations for maximizing performance and minimizing throughput costs\.

## Table Best Practices<a name="BestPractices.Tables"></a>

DynamoDB tables are distributed across multiple partitions\. For best results, design your tables and applications so that read and write activity is spread evenly across all of the items in your tables, and avoid I/O "hot spots" that can degrade performance\.

+ [Design For Uniform Data Access Across Items In Your Tables](GuidelinesForTables.md#GuidelinesForTables.UniformWorkload)

+ [Understand Partition Behavior](GuidelinesForTables.md#GuidelinesForTables.Partitions)

+ [Use Burst Capacity Sparingly](GuidelinesForTables.md#GuidelinesForTables.Bursting)

+ [Distribute Write Activity During Data Upload](GuidelinesForTables.md#GuidelinesForTables.DataUpload)

+ [Understand Access Patterns for Time Series Data](GuidelinesForTables.md#GuidelinesForTables.TimeSeriesDataAccessPatterns)

+ [Cache Popular Items](GuidelinesForTables.md#GuidelinesForTables.CachePopularItems)

+ [Consider Workload Uniformity When Adjusting Provisioned Throughput](GuidelinesForTables.md#GuidelinesForTables.AvoidExcessivePTIncreases)

+ [Test Your Application At Scale](GuidelinesForTables.md#GuidelinesForTables.TestYourAppAtScale)

## Item Best Practices<a name="BestPractices.Items"></a>

DynamoDB items are limited in size \(see [Limits in DynamoDB](Limits.md)\)\. However, there is no limit on the number of items in a table\. Rather than storing large data attribute values in an item, consider one or more of these application design alternatives\.

+ [Use One\-to\-Many Tables Instead Of Large Set Attributes](GuidelinesForItems.md#GuidelinesForItems.OneToMany)

+ [Compress Large Attribute Values](GuidelinesForItems.md#GuidelinesForItems.CompressingLargeAttributeValues)

+ [Store Large Attribute Values in Amazon S3](GuidelinesForItems.md#GuidelinesForItems.StoringInS3)

+ [Break Up Large Attributes Across Multiple Items](GuidelinesForItems.md#GuidelinesForItems.BreakingUpLargeAttribes)

## Query and Scan Best Practices<a name="BestPractices.QueryAndScan"></a>

Sudden, unexpected read activity can quickly consume the provisioned read capacity of a table or a global secondary index\. In addition, such activity can be inefficient if it is not evenly spread across table partitions\.

+ [Performance Considerations for Scans](QueryAndScanGuidelines.md#QueryAndScanGuidelines.ScanPerformance)

+ [Avoid Sudden Spikes in Read Activity](QueryAndScanGuidelines.md#QueryAndScanGuidelines.SpikesOfActivity)

+ [Take Advantage of Parallel Scans](QueryAndScanGuidelines.md#QueryAndScanGuidelines.ParallelScan)

## Local Secondary Index Best Practices<a name="BestPractices.LSI"></a>

A local secondary index lets you define an alternative sort key for your data\. You can query a local secondary index in the same way that you query a table\. Before using local secondary indexes, you should be aware of the inherent tradeoffs in terms of provisioned throughput costs, storage costs, and query efficiency\.

+ [Use Indexes Sparingly](GuidelinesForLSI.md#GuidelinesForLSI.NumberOfIndexes)

+ [Choose Projections Carefully](GuidelinesForLSI.md#GuidelinesForLSI.Projections)

+ [Optimize Frequent Queries To Avoid Fetches](GuidelinesForLSI.md#GuidelinesForLSI.TableFetches)

+ [Take Advantage of Sparse Indexes](GuidelinesForLSI.md#GuidelinesForLSI.SparseIndexes)

+ [Watch For Expanding Item Collections](GuidelinesForLSI.md#GuidelinesForLSI.ItemCollections)

## Global Secondary Index Best Practices<a name="BestPractices.GSI"></a>

Global Secondary Indexes let you define alternative partition key and sort key attributes for your data\. These attributes don't have to be the same as the table's partition key and sort key\. You can query a global secondary index in the same way that you query a table\. As with local secondary indexes, global secondary indexes also present tradeoffs that you need to consider when designing your applications\.

+ [Choose a Key That Will Provide Uniform Workloads](GuidelinesForGSI.md#GuidelinesForGSI.UniformWorkloads)

+ [Take Advantage of Sparse Indexes](GuidelinesForGSI.md#GuidelinesForGSI.SparseIndexes)

+ [Use a Global Secondary Index For Quick Lookups](GuidelinesForGSI.md#GuidelinesForGSI.QuickLookups)

+ [Create an Eventually Consistent Read Replica](GuidelinesForGSI.md#GuidelinesForGSI.DataMirror)