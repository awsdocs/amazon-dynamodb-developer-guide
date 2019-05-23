# Requirements and Best Practices<a name="globaltables_reqs_bestpractices"></a>

Using Amazon DynamoDB global tables, you can replicate your table data across AWS Regions\. It is important that the replica tables and secondary indexes in your global table have identical write capacity settings to ensure proper replication of data\.

**Topics**
+ [Requirements for Adding a New Replica Table](#globaltables_reqs_bestpractices.requirements)
+ [Best Practices and Requirements for Managing Capacity](#globaltables_reqs_bestpractices.tables)

## Requirements for Adding a New Replica Table<a name="globaltables_reqs_bestpractices.requirements"></a>

If you want to add a new replica table to a global table, each of the following conditions must be true:
+ The table must have the same partition key as all of the other replicas\.
+ The table must have the same write capacity management settings specified\.
+ The table must have the same name as all of the other replicas\.
+ The table must have DynamoDB Streams enabled, with the stream containing both the new and the old images of the item\.
+ None of the replica tables in the global table can contain any data\.

 If global secondary indexes are specified, then the following conditions must also be met: 
+  The global secondary indexes must have the same name\. 
+  The global secondary indexes must have the same partition key and sort key \(if present\)\. 

**Important**  
 Write capacity settings should be set consistently across all of your global tables’ replica tables and matching secondary indexes\. To update write capacity settings for your global table, we strongly recommend using the DynamoDB console or the `UpdateGlobalTableSettings` call\. `UpdateGlobalTableSettings` applies changes to write capacity settings to all replica tables and matching secondary indexes in a global table automatically\. If you use the `UpdateTable`, `RegisterScalableTarget`, or `PutScalingPolicy` calls, you should apply the change to each replica table and matching secondary index individually\. For more information, see [Amazon DynamoDB API Reference](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/)\.   
We strongly recommend enabling auto scaling to manage provisioned write capacity settings\. If you prefer to manage write capacity settings manually, you should provision equal replicated write capacity units to all of your replica tables\. You should also provision equal replicated write capacity units to matching secondary indexes across your global table\.   
You must also have appropriate AWS Identity and Access Management \(IAM\) permissions\. For more information, see [Using IAM with Global Tables](gt_IAM.md)\.

## Best Practices and Requirements for Managing Capacity<a name="globaltables_reqs_bestpractices.tables"></a>

Consider the following when managing capacity settings for replica tables in DynamoDB\.

### Using DynamoDB Auto Scaling<a name="globaltables_reqs_bestpractices.tables.autoscaling"></a>

Using DynamoDB auto scaling is the recommended way to manage throughput capacity settings for replica tables that use the provisioned mode\. DynamoDB auto scaling automatically adjusts read capacity units \(RCUs\) and write capacity units \(WCUs\) for each replica table based upon your actual application workload\. For more information, see [Managing Throughput Capacity Automatically with DynamoDB Auto Scaling](AutoScaling.md)\.

If you create your replica tables using the AWS Management Console, auto scaling is enabled by default for each replica table, with default auto scaling settings for managing RCUs and WCUs\.

Changes to auto scaling settings for a replica table or secondary index made through the DynamoDB console or using the `UpdateGlobalTableSettings` call are applied to all of the replica tables and matching secondary indexes in the global table automatically\. These changes will overwrite any existing auto scaling settings\. This ensures that provisioned write capacity settings are consistent across the replica tables and secondary indexes in your global table\. If you use the `UpdateTable`, `RegisterScalableTarget`, or `PutScalingPolicy` calls, you should apply the change to each replica table and matching secondary index individually\. 

**Note**  
 If auto scaling doesn't satisfy your application's capacity changes \(unpredictable workload\) or if you don't want to configure its settings \(target settings for minimum, maximum, or utilization threshold\), you can use on\-demand mode to manage capacity for your global tables\. For more information, see [On\-Demand Mode](HowItWorks.ReadWriteCapacityMode.md#HowItWorks.OnDemand)\.   
If you enable on\-demand mode on a global table, your consumption of replicated write request units \(rWCUs\) will be consistent with how rWCUs are provisioned\. For example, if you perform 10 writes to a local table that is replicated in two additional Regions, you will consume 60 write request units \(10 \+ 10 \+ 10 = 30; 30 x 2 = 60\)\. 

### Managing Capacity Manually<a name="globaltables_reqs_bestpractices.tables.manual-capacity-management"></a>

If you decide not to use DynamoDB auto scaling, then you must manually set the read capacity and write capacity settings on each replica table and secondary index\.

The provisioned replicated write capacity units \(rWCUs\) on every replica table should be set to the total number of rWCUs needed for application writes across all Regions multiplied by two\. This will accommodate application writes that occur in the local Region and replicated application writes coming from other Regions\. For example, if you expect 5 writes per second to your replica table in Ohio and 5 writes per second to your replica table in N\.Virginia, then you should provision 20 WCUs to each replica table \(5 \+ 5 = 10; 10 x 2 = 20\)\.

 To update write capacity settings for your global table, we strongly recommend using the DynamoDB console or the `UpdateGlobalTableSettings` call\. `UpdateGlobalTableSettings` applies changes to write capacity settings to all replica tables and matching secondary indexes in a global table automatically\. If you use the `UpdateTable`, `RegisterScalableTarget`, or `PutScalingPolicy` calls, you should apply the change to each replica table and matching secondary index individually\. For more information, see [Amazon DynamoDB API Reference](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/)\. 

**Note**  
 To update the settings \(`UpdateGlobalTableSettings`\) for a global table in DynamoDB, you must have the `dynamodb:UpdateGlobalTable`, `dynamodb:DescribeLimits`, `application-autoscaling:DeleteScalingPolicy`, and `application-autoscaling:DeregisterScalableTarget` permissions\. For more information, see [Using IAM with Global Tables](gt_IAM.md)\. 