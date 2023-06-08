# CloudWatch contributor insights for DynamoDB: How it works<a name="contributorinsights_HowItWorks"></a>

Amazon DynamoDB integrates with [Amazon CloudWatch Contributor Insights](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/ContributorInsights.html) to provide information about the most accessed and throttled items in a table or global secondary index\. DynamoDB delivers this information to you via CloudWatch Contributor Insights [rules](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/ContributorInsights-RuleSyntax.html), [reports](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/ContributorInsights-ViewReports.html), and graphs of report data\. 

For more information about CloudWatch Contributor Insights, see [Using Contributor Insights to Analyze High\-Cardinality Data](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/ContributorInsights.html) in the *Amazon CloudWatch User Guide*\.

 The following sections describe the core concepts and behavior of CloudWatch Contributor Insights for DynamoDB\. 

**Topics**
+ [CloudWatch contributor insights for DynamoDB rules](#contributorinsights_HowItWorks.Rules)
+ [Understanding CloudWatch contributor insights for DynamoDB graphs](#contributorinsights_HowItWorks.Graphs)
+ [Interactions with other DynamoDB features](#contributorinsights_HowItWorks.OtherFeatures)
+ [CloudWatch contributor insights for DynamoDB billing](#contributorinsights_HowItWorks.Billing)

## CloudWatch contributor insights for DynamoDB rules<a name="contributorinsights_HowItWorks.Rules"></a>

When you enable CloudWatch Contributor Insights for DynamoDB on a table or global secondary index, DynamoDB creates the following [rules](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/ContributorInsights-RuleSyntax.html) on your behalf:
+ **Most accessed items \(partition key\)** — Identifies the partition keys of the most accessed items in your table or global secondary index\.

  CloudWatch rule name format: `DynamoDBContributorInsights-PKC-[resource_name]-[creationtimestamp]`
+ **Most throttled keys \(partition key\)** — Identifies the partition keys of the most throttled items in your table or global secondary index\.

  CloudWatch rule name format: `DynamoDBContributorInsights-PKT-[resource_name]-[creationtimestamp]`

If your table or global secondary index has a sort key, DynamoDB also creates the following rules specific to sort keys: 
+ **Most accessed keys \(partition and sort keys\)** — Identifies the partition and sort keys of the most accessed items in your table or global secondary index\.

  CloudWatch rule name format: `DynamoDBContributorInsights-SKC-[resource_name]-[creationtimestamp]`
+ **Most throttled keys \(partition and sort keys\)** — Identifies the partition and sort keys of the most throttled items in your table or global secondary index\.

  CloudWatch rule name format: `DynamoDBContributorInsights-SKT-[resource_name]-[creationtimestamp]`

**Note**  
You can't use the CloudWatch console or APIs to directly modify or delete the rules created by CloudWatch Contributor Insights for DynamoDB\. Disabling CloudWatch Contributor Insights for DynamoDB on a table or global secondary index automatically deletes the rules created for that table or global secondary index\.
When you use the [GetInsightRuleReport](https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/API_GetInsightRuleReport.html) operation with CloudWatch Contributor Insights rules that are created by DynamoDB, only `MaxContributorValue` and `Maximum` return useful statistics\. The other statistics in this list don't return meaningful values\.

You can create CloudWatch Alarms using the CloudWatch Contributor Insights for DynamoDB [rules](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/ContributorInsights-RuleSyntax.html)\. This allows you to be notified when any item exceed or meets a specific threshold for `ConsumedThroughputUnits` or `ThrottleCount`\. For more information, see [Setting an Alarm on Contributor Insights Metric Data](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/ContributorInsights-GraphReportData.html#ContributorInsights-GraphReportData-Alarm)\.

## Understanding CloudWatch contributor insights for DynamoDB graphs<a name="contributorinsights_HowItWorks.Graphs"></a>

CloudWatch Contributor Insights for DynamoDB displays two types of graphs on both the DynamoDB and CloudWatch consoles: *Most Accessed Items* and *Most Throttled Items*\.

### Most accessed items<a name="contributorinsights_HowItWorks.Graphs.most-accessed"></a>

Use this graph to identify the most accessed items in the table or global secondary index\. The graph displays `ConsumedThroughputUnits` on the y\-axis and time on the x\-axis\. Each of the top N keys is displayed in its own color, with a legend displayed below the x\-axis\. 

DynamoDB measures key access frequency by using `ConsumedThroughputUnits`, which measures combined read and write traffic\. `ConsumedThroughputUnits` is defined as the following: 
+ Provisioned — *\(3 x consumed write capacity units\) \+ consumed read capacity units*
+ On\-demand — *\(3 x write request units\) \+ read request units*

On the DynamoDB console, each data point in the graph represents the maximum of `ConsumedThroughputUnits` over a 1\-minute period\. For example, a graph value of 180,000 `ConsumedThroughputUnits` indicates that the item was accessed continuously at the per\-item maximum throughput of 1,000 write request units or 3,000 read request units for a 60\-second span within that 1\-minute period \(3,000 x 60 seconds\)\. In other words, the graphed values represent the highest\-traffic minute within each 1\-minute period\. You can change the time granularity of the `ConsumedThroughputUnits` metric \(for example, to view 5\-minute metrics instead of 1\-minute\) on the CloudWatch console\.

If you see several closely clustered lines without any obvious outliers, it indicates that your workload is relatively balanced across items over the given time window\. If you see isolated points in the graph instead of connected lines, it indicates an item that was frequently accessed only for a brief period\.

If your table or global secondary index has a sort key, DynamoDB creates two graphs: one for the most accessed partition keys and one for the most accessed partition \+ sort key pairs\. You can see traffic at the partition key level in the partition key–only graph\. You can see traffic at the item level in the partition \+ sort key graphs\.

### Most throttled items<a name="contributorinsights_HowItWorks.Graphs.most-throttled"></a>

Use this graph to identify the most throttled items in the table or global secondary index\. The graph displays `ThrottleCount` on the y\-axis and time on the x\-axis\. Each of the top N keys is displayed in its own color, with a legend displayed below the x\-axis\. 

DynamoDB measures throttle frequency using `ThrottleCount`, which is the count of `ProvisionedThroughputExceededException`, `ThrottlingException`, and `RequestLimitExceeded` errors\.

Write throttling caused by insufficient write capacity for a global secondary index is not measured\. You can use the *Most Accessed Items* graph of the global secondary index to identify imbalanced access patterns that may cause write throttling\. For more information, see [Provisioned Throughput Considerations for Global Secondary Indexes](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GSI.html#GSI.ThroughputConsiderations)\.

On the DynamoDB console, each data point in the graph represents the count of throttle events over a 1\-minute period\. 

If you see no data in this graph, it indicates that your requests are not being throttled\. If you see isolated points in the graph instead of connected lines, it indicates that an item was frequently throttled for a brief period\.

If your table or global secondary index has a sort key, DynamoDB creates two graphs: one for most throttled partition keys and one for most throttled partition \+ sort key pairs\. You can see throttle count at the partition key level in the partition key\-only graph, and throttle count at the item\-level in the partition \+ sort key graphs\.

### Report examples<a name="contributorinsights_HowItWorks.Graphs.examples"></a>

The following are examples of the reports generated for a table with both a partition key and sort key\.

![\[4 different Contributor Insights reports showing most accessed items and most throttled items.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/CI_Graphs_Example.png)

## Interactions with other DynamoDB features<a name="contributorinsights_HowItWorks.OtherFeatures"></a>

The following sections describe how CloudWatch Contributor Insights for DynamoDB behaves and interacts with several other features in DynamoDB\.

### Global tables<a name="contributorinsights_HowItWorks.OtherFeatures"></a>

CloudWatch Contributor Insights for DynamoDB monitors global table replicas as distinct tables\. The Contributor Insights graphs for a replica in one AWS Region might not show the same patterns as another Region\. This is because write data is replicated across all replicas in a global table, but each replica can serve Region\-bound read traffic\. 

### DynamoDB Accelerator \(DAX\)<a name="contributorinsights_HowItWorks.OtherFeatures"></a>

CloudWatch Contributor Insights for DynamoDB doesn't show DAX cache responses\. It only shows responses to accessing a table or a global secondary index\.

**Note**  
DynamoDB CCI does not support PartiQL requests\.

### Encryption at rest<a name="contributorinsights_HowItWorks.OtherFeatures"></a>

CloudWatch Contributor Insights for DynamoDB doesn't affect how encryption works in DynamoDB\. The primary key data that is published in CloudWatch is encrypted with the AWS owned key\. However, DynamoDB also supports the AWS managed key and a customer managed key\.

CloudWatch Contributor Insights for DynamoDB graphs display the partition key and sort key \(if applicable\) of frequently accessed items and frequently throttled items in plaintext\. If you require the use of AWS Key Management Service \(KMS\) to encrypt this table’s partition key and sort key data with an AWS managed key or customer managed key, you should not enable CloudWatch Contributor Insights for DynamoDB for this table\.

If you require your primary key data to be encrypted with the AWS managed key or a customer managed key, you should not enable CloudWatch Contributor Insights for DynamoDB for that table\.

### Fine\-grained access control<a name="contributorinsights_HowItWorks.OtherFeatures"></a>

CloudWatch Contributor Insights for DynamoDB doesn't function differently for tables with fine\-grained access control \(FGAC\)\. In other words, any user who has the appropriate CloudWatch permissions can view FGAC\-protected primary keys in CloudWatch Contributor Insights graphs\. 

If the table’s primary key contains FGAC\-protected data that you don't want published to CloudWatch, you should not enable CloudWatch Contributor Insights for DynamoDB for that table\. 

### Access control<a name="contributorinsights_HowItWorks.OtherFeatures"></a>

You control access to CloudWatch Contributor Insights for DynamoDB using AWS Identity and Access Management \(IAM\) by limiting DynamoDB control plane permissions and CloudWatch data plane permissions\. For more information see, [Using IAM with CloudWatch Contributor Insights for DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Contributor_Insights_IAM.html)\.

## CloudWatch contributor insights for DynamoDB billing<a name="contributorinsights_HowItWorks.Billing"></a>

Charges for CloudWatch Contributor Insights for DynamoDB appear in the [CloudWatch](https://aws.amazon.com/cloudwatch/pricing/) section of your monthly bill\. These charges are calculated based on the number of DynamoDB events that are processed\. For tables and global secondary indexes with CloudWatch Contributor Insights for DynamoDB enabled, each item that is written or read via a [data plane](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.API.html#HowItWorks.API.DataPlane) operation represents one event\.

 If a table or global secondary index includes a sort key, each item that is read or written represents two events\. This is because DynamoDB is identifying top contributors from separate time series: one for partitions keys only, and one for partition and sort key pairs\.

For example, assume that your application performs the following DynamoDB operations: a `GetItem`, a `PutItem`, and a `BatchWriteItem` that puts five items
+ If your table or global secondary index has only a partition key, it results in 7 events \(1 for the `GetItem`, 1 for the `PutItem`, and 5 for the `BatchWriteItem`\)\.
+ If your table or global secondary index has a partition key and sort key, it results in 14 events \(2 for the `GetItem`, 2 for the `PutItem`, and 10 for the `BatchWriteItem`\)\. 
+ A `Query` operation always results in 1 event, regardless of the number of items returned\.

Unlike other DynamoDB features, CloudWatch Contributor Insights for DynamoDB billing *does not* vary based on the following:
+ The [capacity mode](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.ReadWriteCapacityMode.html) \(provisioned vs\. on\-demand\)
+ Whether you perform read or write requests
+ The size \(KB\) of the items read or written