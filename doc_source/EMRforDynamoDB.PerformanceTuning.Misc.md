# Additional topics<a name="EMRforDynamoDB.PerformanceTuning.Misc"></a>

The following are some more ways to tune applications that use Hive to access DynamoDB\.

## Retry duration<a name="EMRforDynamoDB.PerformanceTuning.Misc.RetryDuration"></a>

By default, Hive will rerun a Hadoop job if it has not returned any results from DynamoDB within two minutes\. You can adjust this interval by modifying the `dynamodb.retry.duration` parameter:

```
1. SET dynamodb.retry.duration=2;
```

The value must be a nonzero integer, representing the number of minutes in the retry interval\. The default for `dynamodb.retry.duration` is 2 \(minutes\)\.

## Parallel data requests<a name="EMRforDynamoDB.PerformanceTuning.Misc.ParallelDataRequests"></a>

Multiple data requests, either from more than one user or more than one application to a single table can drain read provisioned throughput and slow performance\. 

## Process duration<a name="EMRforDynamoDB.PerformanceTuning.Misc.ProcessDuration"></a>

Data consistency in DynamoDB depends on the order of read and write operations on each node\. While a Hive query is in progress, another application might load new data into the DynamoDB table or modify or delete existing data\. In this case, the results of the Hive query might not reflect changes made to the data while the query was running\. 

## Request time<a name="EMRforDynamoDB.PerformanceTuning.Misc.RequestTime"></a>

Scheduling Hive queries that access a DynamoDB table when there is lower demand on the DynamoDB table improves performance\. For example, if most of your application's users live in San Francisco, you might choose to export daily data at 4:00 A\.M\. PST when the majority of users are asleep and not updating records in your DynamoDB database\. 

