# DynamoDB Metrics and Dimensions<a name="metrics-dimensions"></a>

When you interact with DynamoDB, it sends the following metrics and dimensions to CloudWatch\. You can use the following procedures to view the metrics for DynamoDB\.

**To view metrics \(console\)**

Metrics are grouped first by the service namespace, and then by the various dimension combinations within each namespace\.

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Metrics**\.

1. Select the **DynamoDB** namespace\.

**To view metrics \(CLI\)**
+ At a command prompt, use the following command:

  ```
  1. aws cloudwatch list-metrics --namespace "AWS/DynamoDB"
  ```

CloudWatch displays the following metrics for DynamoDB:

## Viewing metrics and dimensions<a name="dynamodb-metrics-dimensions"></a>

The metrics and dimensions that DynamoDB sends to Amazon CloudWatch are listed here\.

### DynamoDB Metrics<a name="dynamodb-metrics"></a>

**Note**  
Amazon CloudWatch aggregates these metrics at one\-minute intervals:  
`ConditionalCheckFailedRequests`
`ConsumedReadCapacityUnits`
`ConsumedWriteCapacityUnits`
`ReadThrottleEvents`
`ReturnedBytes`
`ReturnedItemCount`
`ReturnedRecordsCount`
`SuccessfulRequestLatency`
`SystemErrors`
`TimeToLiveDeletedItemCount`
`ThrottledRequests`
`TransactionConflict`
`UserErrors`
`WriteThrottleEvents`
For all other DynamoDB metrics, the aggregation granularity is five minutes\.

Not all statistics, such as *Average* or *Sum*, are applicable for every metric\. However, all of these values are available through the Amazon DynamoDB console, or by using the CloudWatch console, AWS CLI, or AWS SDKs for all metrics\. In the following list, each metric has a set of valid statistics that are applicable to that metric\.

### <a name="available-metrics"></a>

**Topics**<a name="available-metrics.title"></a>
+ [AccountMaxReads](#AccountMaxReads)
+ [AccountMaxTableLevelReads](#AccountMaxTableLevelReads)
+ [AccountMaxTableLevelWrites](#AccountMaxTableLevelWrites)
+ [AccountMaxWrites](#AccountMaxWrites)
+ [AccountProvisionedReadCapacityUtilization](#AccountProvisionedReadCapacityUtilization)
+ [AccountProvisionedWriteCapacityUtilization](#AccountProvisionedWriteCapacityUtilization)
+ [AgeOfOldestUnreplicatedRecord](#AgeOfOldestUnreplicatedRecord)
+ [ConditionalCheckFailedRequests](#ConditionalCheckFailedRequests)
+ [ConsumedChangeDataCaptureUnits](#ConsumedChangeDataCaptureUnits)
+ [ConsumedReadCapacityUnits](#ConsumedReadCapacityUnits)
+ [ConsumedWriteCapacityUnits](#ConsumedWriteCapacityUnits)
+ [FailedToReplicateRecordCount](#FailedToReplicateRecordCount)
+ [MaxProvisionedTableWriteCapacityUtilization](#MaxProvisionedTableWriteCapacityUtilization)
+ [OnlineIndexConsumedWriteCapacity](#OnlineIndexConsumedWriteCapacity)
+ [OnlineIndexPercentageProgress](#OnlineIndexPercentageProgress)
+ [OnlineIndexThrottleEvents](#OnlineIndexThrottleEvents)
+ [PendingReplicationCount](#PendingReplicationCount)
+ [ProvisionedReadCapacityUnits](#ProvisionedReadCapacityUnits)
+ [ProvisionedWriteCapacityUnits](#ProvisionedWriteCapacityUnits)
+ [ReadThrottleEvents](#ReadThrottleEvents)
+ [ReplicationLatency](#ReplicationLatency)
+ [ReturnedBytes](#ReturnedBytes)
+ [ReturnedItemCount](#ReturnedItemCount)
+ [ReturnedRecordsCount](#ReturnedRecordsCount)
+ [SuccessfulRequestLatency](#SuccessfulRequestLatency)
+ [SystemErrors](#SystemErrors)
+ [TimeToLiveDeletedItemCount](#TimeToLiveDeletedItemCount)
+ [ThrottledPutRecordCount](#ThrottledPutRecordCount)
+ [ThrottledRequests](#ThrottledRequests)
+ [TransactionConflict](#TransactionConflict)
+ [UserErrors](#UserErrors)
+ [WriteThrottleEvents](#WriteThrottleEvents)

#### AccountMaxReads<a name="AccountMaxReads"></a>

The maximum number of read capacity units that can be used by an account\. This limit does not apply to on\-demand tables or global secondary indexes\.

Units: `Count`

Valid Statistics:
+ `Maximum` – The maximum number of read capacity units that can be used by an account\.

#### AccountMaxTableLevelReads<a name="AccountMaxTableLevelReads"></a>

The maximum number of read capacity units that can be used by a table or global secondary index of an account\. For on\-demand tables this limit caps the maximum read request units a table or a global secondary index can use\.

Units: `Count`

Valid Statistics:
+ `Maximum` – The maximum number of read capacity units that can be used by a table or global secondary index of the account\.

#### AccountMaxTableLevelWrites<a name="AccountMaxTableLevelWrites"></a>

The maximum number of write capacity units that can be used by a table or global secondary index of an account\. For on\-demand tables this limit caps the maximum write request units a table or a global secondary index can use\.

Units: `Count`

Valid Statistics:
+ `Maximum` – The maximum number of write capacity units that can be used by a table or global secondary index of the account\.

#### AccountMaxWrites<a name="AccountMaxWrites"></a>

The maximum number of write capacity units that can be used by an account\. This limit does not apply to on\-demand tables or global secondary indexes\.

Units: `Count`

Valid Statistics:
+ `Maximum` – The maximum number of write capacity units that can be used by an account\.

#### AccountProvisionedReadCapacityUtilization<a name="AccountProvisionedReadCapacityUtilization"></a>

The percentage of provisioned read capacity units utilized by an account\.

Units: `Percent`

Valid Statistics:
+ `Maximum` – The maximum percentage of provisioned read capacity units utilized by the account\.
+ `Minimum` – The minimum percentage of provisioned read capacity units utilized by the account\.
+ `Average` – The average percentage of provisioned read capacity units utilized by the account\. The metric is published for five\-minute intervals\. Therefore, if you rapidly adjust the provisioned read capacity units, this statistic might not reflect the true average\.

#### AccountProvisionedWriteCapacityUtilization<a name="AccountProvisionedWriteCapacityUtilization"></a>

The percentage of provisioned write capacity units utilized by an account\.

Units: `Percent`

Valid Statistics:
+ `Maximum` – The maximum percentage of provisioned write capacity units utilized by the account\.
+ `Minimum` – The minimum percentage of provisioned write capacity units utilized by the account\.
+ `Average` – The average percentage of provisioned write capacity units utilized by the account\. The metric is published for five\-minute intervals\. Therefore, if you rapidly adjust the provisioned write capacity units, this statistic might not reflect the true average\.

#### AgeOfOldestUnreplicatedRecord<a name="AgeOfOldestUnreplicatedRecord"></a>

The elapsed time since a record yet to be replicated to the Kinesis data stream first appeared in the DynamoDB table\.

Units: `Milliseconds`

Dimensions: `TableName, DelegatedOperation`

Valid Statistics:
+ `Maximum`\.
+ `Minimum`\.
+ `Average`\.

#### ConditionalCheckFailedRequests<a name="ConditionalCheckFailedRequests"></a>

The number of failed attempts to perform conditional writes\. The `PutItem`, `UpdateItem`, and `DeleteItem` operations let you provide a logical condition that must evaluate to true before the operation can proceed\. If this condition evaluates to false, `ConditionalCheckFailedRequests` is incremented by one\. `ConditionalCheckFailedRequests` is also incremented by one for PartiQL Update and Delete statements where a logical condition is provided and that condition evaluates to false\. 

**Note**  
A failed conditional write will result in an HTTP 400 error \(Bad Request\)\. These events are reflected in the `ConditionalCheckFailedRequests` metric, but not in the `UserErrors` metric\.

Units: `Count`

Dimensions: `TableName`

Valid Statistics:
+ `Minimum`
+ `Maximum`
+ `Average`
+ `SampleCount`
+ `Sum`

#### ConsumedChangeDataCaptureUnits<a name="ConsumedChangeDataCaptureUnits"></a>

The number of consumed change data capture units\.

Units: `Count`

Dimensions: `TableName, DelegatedOperation`

Valid Statistics:
+ `Minimum`
+ `Maximum`
+ `Average`

#### ConsumedReadCapacityUnits<a name="ConsumedReadCapacityUnits"></a>

The number of read capacity units consumed over the specified time period, so you can track how much of your provisioned throughput is used\. You can retrieve the total consumed read capacity for a table and all of its global secondary indexes, or for a particular global secondary index\. For more information, see [Read/Write Capacity Mode](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ProvisionedThroughputIntro.html)\.

The `TableName` dimension returns the `ConsumedReadCapacityUnits` for the table, but not for any global secondary indexes\. To view `ConsumedReadCapacityUnits` for a global secondary index, you must specify both `TableName` and `GlobalSecondaryIndex`\.

**Note**  
Use the `Sum` statistic to calculate the consumed throughput\. For example, get the `Sum` value over a span of one minute, and divide it by the number of seconds in a minute \(60\) to calculate the average `ConsumedReadCapacityUnits` per second \(recognizing that this average does not highlight any large but brief spikes in read activity that occurred during that minute\)\. You can compare the calculated value to the provisioned throughput value that you provide DynamoDB\.

Units: `Count`

Dimensions: `TableName, GlobalSecondaryIndexName`

Valid Statistics:
+ `Minimum` – The minimum number of read capacity units consumed by any individual request to the table or index\.
+ `Maximum` – The maximum number of read capacity units consumed by any individual request to the table or index\.
+ `Average` – The average per\-request read capacity consumed\.
**Note**  
The `Average` value is influenced by periods of inactivity where the sample value will be zero\. 
+ `Sum` – The total read capacity units consumed\. This is the most useful statistic for the `ConsumedReadCapacityUnits` metric\.
+ `SampleCount` – The number of read requests to DynamoDB, even if no read capacity was consumed\.
**Note**  
The `SampleCount` value is influenced by periods of inactivity where the sample value will be zero\. 

#### ConsumedWriteCapacityUnits<a name="ConsumedWriteCapacityUnits"></a>

The number of write capacity units consumed over the specified time period, so you can track how much of your provisioned throughput is used\. You can retrieve the total consumed write capacity for a table and all of its global secondary indexes, or for a particular global secondary index\. For more information, see [Read/Write Capacity Mode](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ProvisionedThroughputIntro.html)\.

The `TableName` dimension returns the `ConsumedWriteCapacityUnits` for the table, but not for any global secondary indexes\. To view `ConsumedWriteCapacityUnits` for a global secondary index, you must specify both `TableName` and `GlobalSecondaryIndex`\.

**Note**  
Use the `Sum` statistic to calculate the consumed throughput\. For example, get the `Sum` value over a span of one minute, and divide it by the number of seconds in a minute \(60\) to calculate the average `ConsumedWriteCapacityUnits` per second \(recognizing that this average does not highlight any large but brief spikes in write activity that occurred during that minute\)\. You can compare the calculated value to the provisioned throughput value that you provide DynamoDB\.

Units: `Count`

Dimensions: `TableName, GlobalSecondaryIndexName`

Valid Statistics: 
+ `Minimum` – The minimum number of write capacity units consumed by any individual request to the table or index\.
+ `Maximum` – The maximum number of write capacity units consumed by any individual request to the table or index\.
+ `Average` – The average per\-request write capacity consumed\.
**Note**  
The `Average` value is influenced by periods of inactivity where the sample value will be zero\. 
+ `Sum` – The total write capacity units consumed\. This is the most useful statistic for the `ConsumedWriteCapacityUnits` metric\.
+ `SampleCount` – The number of write requests to DynamoDB, even if no write capacity was consumed\.
**Note**  
The `SampleCount` value is influenced by periods of inactivity where the sample value will be zero\. 

#### FailedToReplicateRecordCount<a name="FailedToReplicateRecordCount"></a>

The number of records that DynamoDB failed to replicate to your Kinesis data stream\.

Units: `Count`

Dimensions: `TableName`, `DelegatedOperation`

Valid Statistics:
+ `Minimum`
+ `Maximum`
+ `Average`
+ `SampleCount`

#### MaxProvisionedTableWriteCapacityUtilization<a name="MaxProvisionedTableWriteCapacityUtilization"></a>

The percentage of provisioned write capacity utilized by the highest provisioned write table or global secondary index of an account\.

Units: `Percent`

Valid Statistics:
+ `Maximum` – The maximum percentage of provisioned write capacity units utilized by the highest provisioned write table or global secondary index of an account\. 
+ `Minimum` – The minimum percentage of provisioned write capacity units utilized by the highest provisioned write table or global secondary index of an account\.
+ `Average` – The average percentage of provisioned write capacity units utilized by the highest provisioned write table or global secondary index of the account\. The metric is published for five\-minute intervals\. Therefore, if you rapidly adjust the provisioned write capacity units, this statistic might not reflect the true average\.

#### OnlineIndexConsumedWriteCapacity<a name="OnlineIndexConsumedWriteCapacity"></a>

The number of write capacity units consumed when adding a new global secondary index to a table\. If the write capacity of the index is too low, incoming write activity during the backfill phase might be throttled\. This can increase the time it takes to create the index\. You should monitor this statistic while the index is being built to determine whether the write capacity of the index is underprovisioned\.

You can adjust the write capacity of the index using the `UpdateTable` operation, even while the index is still being built\.

The `ConsumedWriteCapacityUnits` metric for the index does not include the write throughput consumed during index creation\. 

Units: `Count`

Dimensions: `TableName, GlobalSecondaryIndexName`

Valid Statistics:
+ `Minimum`
+ `Maximum`
+ `Average`
+ `SampleCount`
+ `Sum`

#### OnlineIndexPercentageProgress<a name="OnlineIndexPercentageProgress"></a>

The percentage of completion when a new global secondary index is being added to a table\. DynamoDB must first allocate resources for the new index, and then backfill attributes from the table into the index\. For large tables, this process might take a long time\. You should monitor this statistic to view the relative progress as DynamoDB builds the index\.

Units: `Count`

Dimensions: `TableName, GlobalSecondaryIndexName`

Valid Statistics:
+ `Minimum`
+ `Maximum`
+ `Average`
+ `SampleCount`
+ `Sum`

#### OnlineIndexThrottleEvents<a name="OnlineIndexThrottleEvents"></a>

The number of write throttle events that occur when adding a new global secondary index to a table\. These events indicate that the index creation will take longer to complete, because incoming write activity is exceeding the provisioned write throughput of the index\.

You can adjust the write capacity of the index using the `UpdateTable` operation, even while the index is still being built\.

The `WriteThrottleEvents` metric for the index does not include any throttle events that occur during index creation\. 

Units: `Count`

Dimensions: `TableName, GlobalSecondaryIndexName`

Valid Statistics:
+ `Minimum`
+ `Maximum`
+ `Average`
+ `SampleCount`
+ `Sum`

#### PendingReplicationCount<a name="PendingReplicationCount"></a>

\(This metric is for DynamoDB global tables\.\) The number of item updates that are written to one replica table, but that have not yet been written to another replica in the global table\.

Units: `Count` 

Dimensions: `TableName, ReceivingRegion`

Valid Statistics:
+ `Average`
+ `Sample Count`
+ `Sum`

#### ProvisionedReadCapacityUnits<a name="ProvisionedReadCapacityUnits"></a>

The number of provisioned read capacity units for a table or a global secondary index\.The `TableName` dimension returns the `ProvisionedReadCapacityUnits` for the table, but not for any global secondary indexes\. To view `ProvisionedReadCapacityUnits` for a global secondary index, you must specify both `TableName` and `GlobalSecondaryIndex`\.

Units:`Count`

Dimensions: `TableName, GlobalSecondaryIndexName`

Valid Statistics:
+ `Minimum` – The lowest setting for provisioned read capacity\. If you use `UpdateTable` to increase read capacity, this metric shows the lowest value of provisioned `ReadCapacityUnits` during this time period\.
+ `Maximum` – The highest setting for provisioned read capacity\. If you use `UpdateTable` to decrease read capacity, this metric shows the highest value of provisioned `ReadCapacityUnits` during this time period\.
+ `Average` – The average provisioned read capacity\. The `ProvisionedReadCapacityUnits` metric is published at five\-minute intervals\. Therefore, if you rapidly adjust the provisioned read capacity units, this statistic might not reflect the true average\.

#### ProvisionedWriteCapacityUnits<a name="ProvisionedWriteCapacityUnits"></a>

The number of provisioned write capacity units for a table or a global secondary index\.

The `TableName` dimension returns the `ProvisionedWriteCapacityUnits` for the table, but not for any global secondary indexes\. To view `ProvisionedWriteCapacityUnits` for a global secondary index, you must specify both `TableName` and `GlobalSecondaryIndex`\.

Units: `Count`

Dimensions: `TableName, GlobalSecondaryIndexName`

Valid Statistics:
+ `Minimum` – The lowest setting for provisioned write capacity\. If you use `UpdateTable` to increase write capacity, this metric shows the lowest value of provisioned `WriteCapacityUnits` during this time period\.
+ `Maximum` – The highest setting for provisioned write capacity\. If you use `UpdateTable` to decrease write capacity, this metric shows the highest value of provisioned `WriteCapacityUnits` during this time period\.
+ `Average` – The average provisioned write capacity\. The `ProvisionedWriteCapacityUnits` metric is published at five\-minute intervals\. Therefore, if you rapidly adjust the provisioned write capacity units, this statistic might not reflect the true average\.

#### ReadThrottleEvents<a name="ReadThrottleEvents"></a>

Requests to DynamoDB that exceed the provisioned read capacity units for a table or a global secondary index\.

A single request can result in multiple events\. For example, a `BatchGetItem` that reads 10 items is processed as 10 `GetItem` events\. For each event, `ReadThrottleEvents` is incremented by one if that event is throttled\. The `ThrottledRequests` metric for the entire `BatchGetItem` is not incremented unless *all 10* of the `GetItem` events are throttled\.

The `TableName` dimension returns the `ReadThrottleEvents` for the table, but not for any global secondary indexes\. To view `ReadThrottleEvents` for a global secondary index, you must specify both `TableName` and `GlobalSecondaryIndex`\. 

Units: `Count`

Dimensions: `TableName, GlobalSecondaryIndexName`

Valid Statistics:
+ `SampleCount`
+ `Sum`

#### ReplicationLatency<a name="ReplicationLatency"></a>

\(This metric is for DynamoDB global tables\.\) The elapsed time between an updated item appearing in the DynamoDB stream for one replica table, and that item appearing in another replica in the global table\. 

Units: `Milliseconds` 

Dimensions: `TableName, ReceivingRegion`

Valid Statistics:
+ `Average`
+ `Minimum`
+ `Maximum`

#### ReturnedBytes<a name="ReturnedBytes"></a>

The number of bytes returned by `GetRecords` operations \(Amazon DynamoDB Streams\) during the specified time period\.

Units: `Bytes`

Dimensions: `Operation, StreamLabel, TableName`

Valid Statistics:
+ `Minimum`
+ `Maximum`
+ `Average`
+ `SampleCount`
+ `Sum`

#### ReturnedItemCount<a name="ReturnedItemCount"></a>

The number of items returned by `Query`, `Scan` or `ExecuteStatement` \(select\) operations during the specified time period\.

The number of items *returned* is not necessarily the same as the number of items that were evaluated\. For example, suppose that you requested a `Scan` on a table or an index that had 100 items, but specified a `FilterExpression` that narrowed the results so that only 15 items were returned\. In this case, the response from `Scan` would contain a `ScanCount` of 100 and a `Count` of 15 returned items\.

Units: `Count`

Dimensions: `TableName, Operation`

Valid Statistics:
+ `Minimum`
+ `Maximum`
+ `Average`
+ `SampleCount`
+ `Sum`

#### ReturnedRecordsCount<a name="ReturnedRecordsCount"></a>

The number of stream records returned by `GetRecords` operations \(Amazon DynamoDB Streams\) during the specified time period\.

Units: `Count`

Dimensions: `Operation, StreamLabel, TableName`

Valid Statistics:
+ `Minimum`
+ `Maximum`
+ `Average`
+ `SampleCount`
+ `Sum`

#### SuccessfulRequestLatency<a name="SuccessfulRequestLatency"></a>

The successful requests to DynamoDB or Amazon DynamoDB Streams during the specified time period\. `SuccessfulRequestLatency` can provide two different kinds of information:
+ The elapsed time for successful requests \(`Minimum`, `Maximum`, `Sum`, or `Average`\)\.
+ The number of successful requests \(`SampleCount`\)\.

`SuccessfulRequestLatency` reflects activity only within DynamoDB or Amazon DynamoDB Streams, and does not take into account network latency or client\-side activity\. 

Units: `Milliseconds` 

Dimensions: `TableName, Operation`

Valid Statistics:
+ `Minimum`
+ `Maximum`
+ `Average`
+ `SampleCount`

#### SystemErrors<a name="SystemErrors"></a>

The requests to DynamoDB or Amazon DynamoDB Streams that generate an HTTP 500 status code during the specified time period\. An HTTP 500 usually indicates an internal service error\.

Units: `Count`

Dimensions: `TableName, Operation`

Valid Statistics:
+ `Sum`
+ `SampleCount`

#### TimeToLiveDeletedItemCount<a name="TimeToLiveDeletedItemCount"></a>

The number of items deleted by Time to Live \(TTL\) during the specified time period\. This metric helps you monitor the rate of TTL deletions on your table\. 

Units: `Count`

Dimensions: TableName

Valid Statistics:
+ `Sum`

#### ThrottledPutRecordCount<a name="ThrottledPutRecordCount"></a>

The number of records that were throttled by your Kinesis data stream due to insufficient Kinesis Data Streams capacity\.

Units: `Count`

Dimensions: TableName, DelegatedOperation

Valid Statistics:
+ `Minimum`
+ `Maximum`
+ `Average`
+ `SampleCount`

#### ThrottledRequests<a name="ThrottledRequests"></a>

Requests to DynamoDB that exceed the provisioned throughput limits on a resource \(such as a table or an index\)\.

`ThrottledRequests` is incremented by one if any event within a request exceeds a provisioned throughput limit\. For example, if you update an item in a table with global secondary indexes, there are multiple events—a write to the table, and a write to each index\. If one or more of these events are throttled, then `ThrottledRequests` is incremented by one\.

**Note**  
In a batch request \(`BatchGetItem` or `BatchWriteItem`\), `ThrottledRequests` is incremented only if *every* request in the batch is throttled\.  
If any individual request within the batch is throttled, one of the following metrics is incremented:  
`ReadThrottleEvents` – For a throttled `GetItem` event within `BatchGetItem`\.
`WriteThrottleEvents` – For a throttled `PutItem` or `DeleteItem` event within `BatchWriteItem`\.

To gain insight into which event is throttling a request, compare `ThrottledRequests` with the `ReadThrottleEvents` and `WriteThrottleEvents` for the table and its indexes\.

**Note**  
A throttled request will result in an HTTP 400 status code\. All such events are reflected in the `ThrottledRequests` metric, but not in the `UserErrors` metric\.

Units: `Count`

Dimensions: `TableName, Operation`

Valid Statistics:
+ `Sum`
+ `SampleCount`

#### TransactionConflict<a name="TransactionConflict"></a>

Rejected item\-level requests due to transactional conflicts between concurrent requests on the same items\. For more information, see [Transaction Conflict Handling in DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/transaction-apis.html#transaction-conflict-handling)\. 

Units: `Count`

Dimensions: `TableName`

Valid Statistics:
+ `Sum` – The number of rejected item\-level requests due to transaction conflicts\. 
**Note**  
If multiple item\-level requests within a call to `TransactWriteItems` or `TransactGetItems` were rejected, `Sum` is incremented by one for each item\-level `Put`, `Update`, `Delete`, or `Get` request\.
+ `SampleCount` – The number of rejected requests due to transaction conflicts\.
**Note**  
If multiple item\-level requests within a call to `TransactWriteItems` or `TransactGetItems` are rejected, `SampleCount` is only incremented by one\.
+ `Min` – The minimum number of rejected item\-level requests within a call to `TransactWriteItems`, `TransactGetItems`, `PutItem`, `UpdateItem`, or `DeleteItem`\.
+ `Max` – The maximum number of rejected item\-level requests within a call to `TransactWriteItems`, `TransactGetItems`, `PutItem`, `UpdateItem`, or `DeleteItem`\.
+ `Average` – The average number of rejected item\-level requests within a call to `TransactWriteItems`, `TransactGetItems`, `PutItem`, `UpdateItem`, or `DeleteItem`\. 

#### UserErrors<a name="UserErrors"></a>

Requests to DynamoDB or Amazon DynamoDB Streams that generate an HTTP 400 status code during the specified time period\. An HTTP 400 usually indicates a client\-side error, such as an invalid combination of parameters, an attempt to update a nonexistent table, or an incorrect request signature\.

All such events are reflected in the `UserErrors` metric, except for the following:
+ *ProvisionedThroughputExceededException* – See the `ThrottledRequests` metric in this section\.
+ *ConditionalCheckFailedException* – See the `ConditionalCheckFailedRequests` metric in this section\.

`UserErrors` represents the aggregate of HTTP 400 errors for DynamoDB or Amazon DynamoDB Streams requests for the current AWS Region and the current AWS account\.

Units: `Count`

Valid Statistics:
+ `Sum`
+ `SampleCount`

#### WriteThrottleEvents<a name="WriteThrottleEvents"></a>

Requests to DynamoDB that exceed the provisioned write capacity units for a table or a global secondary index\.

A single request can result in multiple events\. For example, a `PutItem` request on a table with three global secondary indexes would result in four events—the table write, and each of the three index writes\. For each event, the `WriteThrottleEvents` metric is incremented by one if that event is throttled\. For single `PutItem` requests, if any of the events are throttled, `ThrottledRequests` is also incremented by one\. For `BatchWriteItem`, the `ThrottledRequests` metric for the entire `BatchWriteItem` is not incremented unless all of the individual `PutItem` or `DeleteItem` events are throttled\.

The `TableName` dimension returns the `WriteThrottleEvents` for the table, but not for any global secondary indexes\. To view `WriteThrottleEvents` for a global secondary index, you must specify both `TableName` and `GlobalSecondaryIndex`\. 

Units: `Count`

Dimensions: `TableName, GlobalSecondaryIndexName`

Valid Statistics:
+ `Sum`
+ `SampleCount`

## Understanding metrics and dimensions for DynamoDB<a name="ddb-understanding-metric-dimensions"></a>

The metrics for DynamoDB are qualified by the values for the account, table name, global secondary index name, or operation\. You can use the CloudWatch console to retrieve DynamoDB data along any of the dimensions in the table below\.

**Topics**<a name="available-metrics.title"></a>
+ [DelegatedOperation](#w615aac30b7c17b7c13b7)
+ [GlobalSecondaryIndexName](#w615aac30b7c17b7c13b9)
+ [Operation](#w615aac30b7c17b7c13c11)
+ [OperationType](#w615aac30b7c17b7c13c13)
+ [Verb](#w615aac30b7c17b7c13c15)
+ [ReceivingRegion](#w615aac30b7c17b7c13c17)
+ [StreamLabel](#w615aac30b7c17b7c13c19)
+ [TableName](#w615aac30b7c17b7c13c21)

### DelegatedOperation<a name="w615aac30b7c17b7c13b7"></a>

This dimension limits the data to operations DynamoDB performs on your behalf\. The following operations are captured:
+ Change data capture for Kinesis Data Streams\.

### GlobalSecondaryIndexName<a name="w615aac30b7c17b7c13b9"></a>

This dimension limits the data to a global secondary index on a table\. If you specify `GlobalSecondaryIndexName`, you must also specify `TableName`\.

### Operation<a name="w615aac30b7c17b7c13c11"></a>

This dimension limits the data to one of the following DynamoDB operations:
+ `PutItem`
+ `DeleteItem`
+ `UpdateItem`
+ `GetItem`
+ `BatchGetItem`
+ `Scan`
+ `Query`
+ `BatchWriteItem`
+ `TransactWriteItems`
+ `TransactGetItems`
+ `ExecuteTransaction`
+ `BatchExecuteStatement`
+ `ExecuteStatement`

In addition, you can limit the data to the following Amazon DynamoDB Streams operation:
+ `GetRecords`

### OperationType<a name="w615aac30b7c17b7c13c13"></a>

This dimension limits the data to one of the following operation types:
+ `Read`
+ `Write`

This dimension is emitted for `ExecuteTransaction` and `BatchExecuteStatement` requests\.

### Verb<a name="w615aac30b7c17b7c13c15"></a>

This dimension limits the data to one of the following DynamoDB PartiQL verbs:
+ Insert: `PartiQLInsert`
+ Select: `PartiQLSelect`
+ Update: `PartiQLUpdate`
+ Delete: `PartiQLDelete`

This dimension is emitted for the `ExecuteStatement` operation\.

### ReceivingRegion<a name="w615aac30b7c17b7c13c17"></a>

This dimension limits the data to a particular AWS region\. It is used with metrics originating from replica tables within a DynamoDB global table\.

### StreamLabel<a name="w615aac30b7c17b7c13c19"></a>

This dimension limits the data to a specific stream label\. It is used with metrics originating from Amazon DynamoDB Streams `GetRecords` operations\.

### TableName<a name="w615aac30b7c17b7c13c21"></a>

This dimension limits the data to a specific table\. This value can be any table name in the current region and the current AWS account\.