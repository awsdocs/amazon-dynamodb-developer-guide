# Adjusting the mappers<a name="EMRforDynamoDB.PerformanceTuning.Mappers"></a>

When Hive launches a Hadoop job, the job is processed by one or more mapper tasks\. Assuming that your DynamoDB table has sufficient throughput capacity, you can modify the number of mappers in the cluster, potentially improving performance\.

**Note**  
The number of mapper tasks used in a Hadoop job are influenced by *input splits*, where Hadoop subdivides the data into logical blocks\. If Hadoop does not perform enough input splits, then your write operations might not be able to consume all the write throughput available in the DynamoDB table\. 

## Increasing the number of mappers<a name="EMRforDynamoDB.PerformanceTuning.Mappers.Increasing"></a>

Each mapper in an Amazon EMR has a maximum read rate of 1 MiB per second\. The number of mappers in a cluster depends on the size of the nodes in your cluster\. \(For information about node sizes and the number of mappers per node, see [Task Configuration](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-hadoop-task-config.html) in the *Amazon EMR Developer Guide*\.\)

If your DynamoDB table has ample throughput capacity for reads, you can try increasing the number of mappers by doing one of the following:
+ Increase the size of the nodes in your cluster\. For example, if your cluster is using *m1\.large* nodes \(three mappers per node\), you can try upgrading to *m1\.xlarge* nodes \(eight mappers per node\)\.
+ Increase the number of nodes in your cluster\. For example, if you have three\-node cluster of *m1\.xlarge* nodes, you have a total of 24 mappers available\. If you were to double the size of the cluster, with the same type of node, you would have 48 mappers\.

You can use the AWS Management Console to manage the size or the number of nodes in your cluster\. \(You might need to restart the cluster for these changes to take effect\.\)

Another way to increase the number of mappers is to modify the `mapred.tasktracker.map.tasks.maximum` Hadoop configuration parameter\. \(This is a Hadoop parameter, not a Hive parameter\. You cannot modify it interactively from the command prompt\.\)\. If you increase the value of `mapred.tasktracker.map.tasks.maximum`, you can increase the number of mappers without increasing the size or number of nodes\. However, it is possible for the cluster nodes to run out of memory if you set the value too high\.

You set the value for `mapred.tasktracker.map.tasks.maximum` as a bootstrap action when you first launch your Amazon EMR cluster\. For more information, see [\(Optional\) Create Bootstrap Actions to Install Additional Software](https://docs.aws.amazon.com/ElasticMapReduce/latest/ManagementGuide/emr-plan-bootstrap.html) in the *Amazon EMR Management Guide*\.

## Decreasing the number of mappers<a name="EMRforDynamoDB.PerformanceTuning.Mappers.Decreasing"></a>

If you use the `SELECT` statement to select data from an external Hive table that maps to DynamoDB, the Hadoop job can use as many tasks as necessary, up to the maximum number of mappers in the cluster\. In this scenario, it is possible that a long\-running Hive query can consume all of the provisioned read capacity of the DynamoDB table, negatively impacting other users\.

You can use the `dynamodb.max.map.tasks` parameter to set an upper limit for map tasks:

```
SET dynamodb.max.map.tasks=1
```

This value must be equal to or greater than 1\. When Hive processes your query, the resulting Hadoop job will use no more than `dynamodb.max.map.tasks` when reading from the DynamoDB table\.