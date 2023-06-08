# DynamoDB provisioned throughput<a name="EMRforDynamoDB.PerformanceTuning.Throughput"></a>

When you issue HiveQL statements against the external DynamoDB table, the `DynamoDBStorageHandler` class makes the appropriate low\-level DynamoDB API requests, which consume provisioned throughput\. If there is not enough read or write capacity on the DynamoDB table, the request will be throttled, resulting in slow HiveQL performance\. For this reason, you should ensure that the table has enough throughput capacity\.

For example, suppose that you have provisioned 100 read capacity units for your DynamoDB table\. This will let you read 409,600 bytes per second \(100 × 4 KB read capacity unit size\)\. Now suppose that the table contains 20 GB of data \(21,474,836,480 bytes\) and you want to use the `SELECT` statement to select all of the data using HiveQL\. You can estimate how long the query will take to run like this:

 * 21,474,836,480 / 409,600 = 52,429 seconds = 14\.56 hours * 

In this scenario, the DynamoDB table is a bottleneck\. It won't help to add more Amazon EMR nodes, because the Hive throughput is constrained to only 409,600 bytes per second\. The only way to decrease the time required for the `SELECT` statement is to increase the provisioned read capacity of the DynamoDB table\. 

You can perform a similar calculation to estimate how long it would take to bulk\-load data into a Hive external table mapped to a DynamoDB table\. Determine the total number of bytes in the data you want to load, and then divide it by the size of one DynamoDB write capacity unit \(1 KB\)\. This will yield the number of seconds it will take to load the table\.

You should regularly monitor the CloudWatch metrics for your table\. For a quick overview in the DynamoDB console, choose your table and then choose the **Metrics** tab\. From here, you can view read and write capacity units consumed and read and write requests that have been throttled\.

## Read capacity<a name="EMRforDynamoDB.PerformanceTuning.Throughput.ReadCapacity"></a>

Amazon EMR manages the request load against your DynamoDB table, according to the table's provisioned throughput settings\. However, if you notice a large number of `ProvisionedThroughputExceeded` messages in the job output, you can adjust the default read rate\. To do this, you can modify the `dynamodb.throughput.read.percent` configuration variable\. You can use the `SET` command to set this variable at the Hive command prompt:

```
1. SET dynamodb.throughput.read.percent=1.0;
```

This variable persists for the current Hive session only\. If you exit Hive and return to it later, `dynamodb.throughput.read.percent` will return to its default value\.

The value of `dynamodb.throughput.read.percent` can be between `0.1` and `1.5`, inclusively\. `0.5` represents the default read rate, meaning that Hive will attempt to consume half of the read capacity of the table\. If you increase the value above `0.5`, Hive will increase the request rate; decreasing the value below `0.5` decreases the read request rate\. \(The actual read rate will vary, depending on factors such as whether there is a uniform key distribution in the DynamoDB table\.\)

If you notice that Hive is frequently depleting the provisioned read capacity of the table, or if your read requests are being throttled too much, try reducing `dynamodb.throughput.read.percent` below `0.5`\. If you have sufficient read capacity in the table and want more responsive HiveQL operations, you can set the value above `0.5`\.

## Write capacity<a name="EMRforDynamoDB.PerformanceTuning.Throughput.WriteCapacity"></a>

Amazon EMR manages the request load against your DynamoDB table, according to the table's provisioned throughput settings\. However, if you notice a large number of `ProvisionedThroughputExceeded` messages in the job output, you can adjust the default write rate\. To do this, you can modify the `dynamodb.throughput.write.percent` configuration variable\. You can use the `SET` command to set this variable at the Hive command prompt:

```
1. SET dynamodb.throughput.write.percent=1.0;
```

This variable persists for the current Hive session only\. If you exit Hive and return to it later, `dynamodb.throughput.write.percent` will return to its default value\.

The value of `dynamodb.throughput.write.percent` can be between `0.1` and `1.5`, inclusively\. `0.5` represents the default write rate, meaning that Hive will attempt to consume half of the write capacity of the table\. If you increase the value above `0.5`, Hive will increase the request rate; decreasing the value below `0.5` decreases the write request rate\. \(The actual write rate will vary, depending on factors such as whether there is a uniform key distribution in the DynamoDB table\.\)

If you notice that Hive is frequently depleting the provisioned write capacity of the table, or if your write requests are being throttled too much, try reducing `dynamodb.throughput.write.percent` below `0.5`\. If you have sufficient capacity in the table and want more responsive HiveQL operations, you can set the value above `0.5`\.

When you write data to DynamoDB using Hive, ensure that the number of write capacity units is greater than the number of mappers in the cluster\. For example, consider an Amazon EMR cluster consisting of 10 *m1\.xlarge* nodes\. The *m1\.xlarge* node type provides 8 mapper tasks, so the cluster would have a total of 80 mappers \(10 × 8\)\. If your DynamoDB table has fewer than 80 write capacity units, then a Hive write operation could consume all of the write throughput for that table\.

To determine the number of mappers for Amazon EMR node types, see [Task Configuration](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-hadoop-task-config.html) in the *Amazon EMR Developer Guide*\.

For more information on mappers, see [Adjusting the mappers](EMRforDynamoDB.PerformanceTuning.Mappers.md)\.