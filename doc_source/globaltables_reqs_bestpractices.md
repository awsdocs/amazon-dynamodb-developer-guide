# Requirements and Best Practices<a name="globaltables_reqs_bestpractices"></a>

A DynamoDB global table keeps your data consistent across regions\. In this environment, it is important that each replica table has identical properties\.

**Topics**
+ [Requirements for Adding a New Replica Table](#globaltables_reqs_bestpractices.requirements)
+ [Best Practices for Managing Capacity](#globaltables_reqs_bestpractices.tables)
+ [Best Practices for Global Secondary Indexes](#globaltables_reqs_bestpractices.gsi)

## Requirements for Adding a New Replica Table<a name="globaltables_reqs_bestpractices.requirements"></a>

If you want to add a new replica table to a global table, each of the following conditions must be true:
+ The table must have the same primary key as all of the other replicas\.
+ The table must have the same name as all of the other replicas\.
+ The table must have DynamoDB Streams enabled, with the stream containing both the new and the old images of the item\.
+ None of the replica tables in the global table can contain any data\.

You must also have appropriate IAM permissions\. For more information, see [Using IAM with Global Tables](gt_IAM.md)\.

## Best Practices for Managing Capacity<a name="globaltables_reqs_bestpractices.tables"></a>

### Use DynamoDB Auto Scaling \(Recommended\)<a name="globaltables_reqs_bestpractices.tables.autoscaling"></a>

Using DynamoDB auto scaling is the recommended way to manage capacity unit settings on replica tables, and ensure that the replica tables' settings remain identical\. \(For more information, see [Managing Throughput Capacity Automatically with DynamoDB Auto Scaling](AutoScaling.md)\.\) DynamoDB auto scaling automatically adjusts read capacity units \(RCUs\) and write capacity units \(WCUs\) on each replica table, based upon your actual application workload\.

If you create your replica tables using the AWS Management Console, then auto scaling will be enabled by default for each replica table, with default auto scaling settings for managing RCUs and WCUs\.

If you decide to modify the auto scaling settings on one replica table, you should ensure that you also apply the same auto scaling settings on all of the other replica tables\. This will ensure that capacity unit management is consistent across all of the replica tables\.

### Keep Capacity Settings Consistent<a name="globaltables_reqs_bestpractices.tables.consistent-settings"></a>

For a global table, DynamoDB does not require that all of its replicas have the same throughput settings\. However, for optimum performance, we strongly recommend that you keep the read and write capacity consistent across the replica tables\.

To illustrate the importance of consistent capacity unit settings, suppose that you create a global table consisting of replicas in US West \(Oregon\) and EU \(Ireland\)\. Now suppose that you set the write capacity units for the US West \(Oregon\) replica to 1000, but only 500 write capacity units for the EU \(Ireland\) replica\. If your application performs more than 500 writes per second to the US West \(Oregon\) replica, then the EU \(Ireland\) replica will not be able to keep up\. The data in these two replica tables will diverge, as EU \(Ireland\) replica falls further behind—perhaps indefinitely\.

### Manually Managing Capacity<a name="globaltables_reqs_bestpractices.tables.manual-capacity-management"></a>

If you decide not to use DynamoDB auto scaling, then you will need to manually set the read capacity and write capacity settings on each replica table\.

Provisioned replicated write capacity units \(rWCUs\) in each region for every replica table should be the total number of rWCUs needed for application writes in all regions multiplied by two\. This will accommodate application writes that occur in the local region; replicated application writes coming from other regions; and at least one additional system write for each application write\. \(DynamoDB performs these system writes on your behalf, to support the "last writer wins" conflict resolution mechanism provided by global tables\.\)

## Best Practices for Global Secondary Indexes<a name="globaltables_reqs_bestpractices.gsi"></a>

For a global table, if you create one or more global secondary indexes on one replica, then you should create identical indexes on every replica\. The read capacity units \(RCUs\) and write capacity units \(WCUs\) for each global secondary index should likewise be identical on every replica\.