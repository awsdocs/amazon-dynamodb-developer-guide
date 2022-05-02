# Best Practices and Requirements for Managing Global Tables<a name="V2globaltables_reqs_bestpractices"></a>


****  

|  | 
| --- |
| There are two versions of DynamoDB global tables available: [Version 2019\.11\.21 \(Current\)](globaltables.V2.md) and [Version 2017\.11\.29](globaltables.V1.md)\. To find out which version you are using, see [Determining the version](globaltables.DetermineVersion.md)\. | 

Using Amazon DynamoDB global tables, you can replicate your table data across AWS Regions\. It is important that the replica tables and secondary indexes in your global table have identical write capacity settings to ensure proper replication of data\.

## Best Practices and Requirements for Managing Capacity<a name="V2globaltables_reqs_bestpractices.tables"></a>

Consider the following when managing capacity settings for replica tables in DynamoDB\.

When using the latest version of global tables, you must either use on\-demand capacity or enable autoscaling on the table\. Using autoscaling or on\-demand capacity ensures that you always have sufficient capacity to perform replicated writes to all regions of the global table\. The number of replicated write capacity units \(rWCUs\) or replicated write request units will be equal in all Regions of the global table\. For example, suppose that you expect 5 writes per second to your replica table in Ohio and 5 writes per second to your replica table in N\. Virginia\. In this case, you should expect to consume 10 rWCUs \(or 10 replicated write request units, if using on\-demand capacity\) in each region, Ohio and N\. Virginia\. In other words, you should expect to consume 20 rWCUs total across both Ohio and N\. Virginia\. When you use the provisioned capacity mode, you manage your auto scaling policy with UpdateTableReplicaAutoScaling\. Minimum and maximum throughput and target utilization are established globally for the table and passed to all replicas of the table\.

For details about autoscaling and DynamoDB, see [Managing Throughput Capacity Automatically with DynamoDB Auto Scaling](AutoScaling.md)\. 