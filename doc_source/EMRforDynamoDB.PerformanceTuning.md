# Performance Tuning<a name="EMRforDynamoDB.PerformanceTuning"></a>

When you create a Hive external table that maps to a DynamoDB table, you do not consume any read or write capacity from DynamoDB\. However, read and write activity on the Hive table \(such as `INSERT` or `SELECT`\) translates directly into read and write operations on the underlying DynamoDB table\.

Apache Hive on Amazon EMR implements its own logic for balancing the I/O load on the DynamoDB table and seeks to minimize the possibility of exceeding the table's provisioned throughput\. At the end of each Hive query, Amazon EMR returns runtime metrics, including the number of times your provisioned throughput was exceeded\. You can use this information, together with CloudWatch metrics on your DynamoDB table, to improve performance in subsequent requests\.

The Amazon EMR console provides basic monitoring tools for your cluster\. For more information, see [View and Monitor a Cluster](https://docs.aws.amazon.com/ElasticMapReduce/latest/ManagementGuide/emr-manage-view.html) in the *Amazon EMR Management Guide*\.

You can also monitor your cluster and Hadoop jobs using web\-based tools, such as Hue, Ganglia, and the Hadoop web interface\. For more information, see [View Web Interfaces Hosted on Amazon EMR Clusters](https://docs.aws.amazon.com/ElasticMapReduce/latest/ManagementGuide/emr-web-interfaces.html) in the *Amazon EMR Management Guide*\.

This section describes steps you can take to performance\-tune Hive operations on external DynamoDB tables\. 

**Topics**
+ [DynamoDB Provisioned Throughput](EMRforDynamoDB.PerformanceTuning.Throughput.md)
+ [Adjusting the Mappers](EMRforDynamoDB.PerformanceTuning.Mappers.md)
+ [Additional Topics](EMRforDynamoDB.PerformanceTuning.Misc.md)