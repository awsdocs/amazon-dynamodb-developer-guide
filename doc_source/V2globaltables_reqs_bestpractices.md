# Best practices and requirements for managing global tables<a name="V2globaltables_reqs_bestpractices"></a>

Using Amazon DynamoDB global tables, you can replicate your table data across AWS Regions\. It is important that the replica tables and secondary indexes in your global table have identical write capacity settings to ensure proper replication of data\.

For future clarity, it can be useful not to put the Region in the name for any table that might someday be turned into a global table\.

**Warning**  
The table name for each global table must be unique within your AWS account\. 

## Global tables version<a name="V2globaltables_version.tables"></a>

There are two versions of DynamoDB global tables available: [Version 2019\.11\.21 \(Current\)](globaltables.V2.md) and [Version 2017\.11\.29 \(Legacy\)](globaltables.V1.md)\. Customers should use Version 2019\.11\.21 \(Current\) when possible, as it provides greater flexibility, higher efficiency and consumes less write capacity than Version 2017\.11\.29 \(Legacy\)\. To determine which version you are using, see [Determining the global table version you are using](globaltables.DetermineVersion.md)\. 

## Requirements for Managing Capacity<a name="V2globaltables_reqs_bestpractices.tables"></a>

A global table must have throughput capacity configured one of two ways:

1. On\-demand capacity mode, measured in replicated write request units \(rWRUs\)

1. Provisioned capacity mode with auto scaling, measured in replicated write capacity units \(rWCUs\)

Using provisioned capacity mode with auto scaling or on\-demand capacity mode helps ensure a global table has sufficient capacity to perform replicated writes to all regions of the global table\.

**Note**  
Switching from one table capacity mode to the other capacity mode in any Region switches the mode for all replicas\.

## Deploying global tables<a name="deploying"></a>

In AWS CloudFormation, each global table is controlled by a single stack in a single Region\. This is regardless of the number of replicas\. When you deploy your template, CloudFormation will create/update all replicas as part of a single stack operation\. For this reason, you should not deploy the same `AWS::DynamoDB::GlobalTable` resource in multiple Regions\. Doing so is unsupported and will result in errors\.

If you deploy your application template in multiple Regions, you can use conditions to create the resource in only one Region\. Alternatively, you can choose to define your `AWS::DynamoDB::GlobalTable` resources in a stack separate from your application stack and make sure it is only deployed to a single Region\. For more information see [ Global tables in CloudFormation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-dynamodb-globaltable.html) 

A DynamoDB table is referred to by `AWS::DynamoDB::Table` , and a global table is `AWS::DynamoDB::GlobalTable`\. As far as CloudFormation is concerned, this essentially makes them two different resources\. As a result, one approach is to create all tables that might ever be global by using the `GlobalTable` construct\. You can then keep them as standalone tables to start, and add them later to Regions if needed\.

If you have a regular table and you want to convert it while using CloudFormation, a recommended method is to: 

1. Set the deletion policy to retain\.

1. Remove the table from the stack\.

1. Convert the table to a Global Table in the console\.

1. Import the global table as a new resource to the stack\.

**Note**  
Cross\-account replication is not supported at this time\.

## Using global tables to help handle a potential Region outage<a name="outage"></a>

Have or be able to quickly create independent copies of your execution stack in alternative Regions, each accessing its local DynamoDB endpoint\. 

Use Route53 or AWS Global Accelerator to route to the nearest healthy Region\. Alternately, have the client aware of the multiple endpoints it might use\. 

Use health checks in each Region that will be able to determine reliably if there’s any issue with the stack, including if DynamoDB is degraded\. For example, don’t just ping that the DynamoDB endpoint is up\. Actually do a call that ensures a full successful database flow\.

If the health check fail, traffic can route to other Regions \(by updating the DNS entry with Route53, by having Global Accelerator route differently, or by having the client choose a different endpoint\)\. Global tables have a good RPO \(recovery point objective\) because the data is continuously syncing and a good RTO \(recovery time objective\) because both Regions always keep a table ready for both read and write traffic\.

For further information on health checks see [Health check types](/Route53/latest/DeveloperGuide/health-checks-types.html)\. 

**Note**  
DynamoDB is a core service on which other services frequently build their control plane operations, so it’s unlikely you’ll encounter a scenario where DynamoDB has degraded service in a Region while other services are unimpacted\.

## Backing up global tables<a name="backup"></a>

When backing up global tables, a backup of tables in one Region should be sufficient and backing up all tables in all Regions shouldn't be required\. If the purpose is to be able to recover erroneously deleted or modified data, then PITR in one Region should suffice\. Similarly, when preserving a snapshot for historic purposes such as regulatory requirements then backing up in one Region should suffice\. The backed up data can be replicated to multiple Regions via AWS Backup\. 

## Replicas and calculating write units<a name="calculating_write_units"></a>

For planning, you should take the number of writes that one Region will perform and add that to the number of writes happening for each other Region\. This is critical as every write that is performed in one Region must also be performed in every replica Region\. If you do not have enough capacity to handle all of the writes, capacity exceptions will occur\. In addition, inter\-regional replication wait times will rise\.

For example, suppose that you expect 5 writes per second to your replica table in Ohio, 10 writes per second to your replica table in N\. Virginia, and 5 writes per second to your replica table in Ireland\. In this case, you should expect to consume 20 rWCUs or rWRUs in each Region: Ohio, N\. Virginia, and Ireland\. In other words, you should expect to consume 60 rWCUs total across all three Regions\.

For details about provisioned capacity with auto scaling and DynamoDB, see [Managing throughput capacity automatically with DynamoDB auto scaling](AutoScaling.md)\. 

**Note**  
If a table is running in provisioned capacity mode with auto scaling, the provisioned write capacity is allowed to float within those autoscaling settings for each Region\. 