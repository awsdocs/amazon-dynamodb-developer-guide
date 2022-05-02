# Logging DynamoDB Operations by Using AWS CloudTrail<a name="logging-using-cloudtrail"></a>

DynamoDB is integrated with AWS CloudTrail, a service that provides a record of actions taken by a user, role, or an AWS service in DynamoDB\. CloudTrail captures all API calls for DynamoDB as events\. The calls captured include calls from the DynamoDB console and code calls to the DynamoDB API operations, using both PartiQL and the classic API\. If you create a trail, you can enable continuous delivery of CloudTrail events to an Amazon S3 bucket, including events for DynamoDB\. If you don't configure a trail, you can still view the most recent events in the CloudTrail console in **Event history**\. Using the information collected by CloudTrail, you can determine the request that was made to DynamoDB, the IP address from which the request was made, who made the request, when it was made, and additional details\.

For robust monitoring and alerting, you can also integrate CloudTrail events with [Amazon CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html)\. To enhance your analysis of DynamoDB service activity and identify changes in activities for an AWS account, you can query AWS CloudTrail logs using [Amazon Athena](https://docs.aws.amazon.com/athena/latest/ug/cloudtrail-logs.html)\. For example, you can use queries to identify trends and further isolate activity by attributes such as source IP address or user\.

To learn more about CloudTrail, including how to configure and enable it, see the [AWS CloudTrail User Guide](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/)\.

**Topics**
+ [DynamoDB Information in CloudTrail](#service-name-info-in-cloudtrail)
+ [Understanding DynamoDB Log File Entries](understanding-ddb-log-entries.md)

## DynamoDB Information in CloudTrail<a name="service-name-info-in-cloudtrail"></a>

CloudTrail is enabled on your AWS account when you create the account\. When supported event activity occurs in DynamoDB, that activity is recorded in a CloudTrail event along with other AWS service events in **Event history**\. You can view, search, and download recent events in your AWS account\. For more information, see [Viewing Events with CloudTrail Event History](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/view-cloudtrail-events.html)\.

For an ongoing record of events in your AWS account, including events for DynamoDB, create a trail\. A *trail* enables CloudTrail to deliver log files to an Amazon S3 bucket\. By default, when you create a trail in the console, the trail applies to all AWS Regions\. The trail logs events from all Regions in the AWS partition and delivers the log files to the Amazon S3 bucket that you specify\. Additionally, you can configure other AWS services to further analyze and act upon the event data collected in CloudTrail logs\. For more information, see the following:
+ [Overview for Creating a Trail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-and-update-a-trail.html)
+ [CloudTrail Supported Services and Integrations](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-aws-service-specific-topics.html#cloudtrail-aws-service-specific-topics-integrations)
+ [Configuring Amazon SNS Notifications for CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/getting_notifications_top_level.html)
+ [Receiving CloudTrail Log Files from Multiple Regions](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/receive-cloudtrail-log-files-from-multiple-regions.html) and [Receiving CloudTrail Log Files from Multiple Accounts](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-receive-logs-from-multiple-accounts.html)

### Control Plane Events in CloudTrail<a name="ddb-control-plane-events-in-cloudtrail"></a>

The following API actions are logged by default as events in CloudTrail files:

**Amazon DynamoDB**
+ [CreateBackup](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_CreateBackup.html)
+ [CreateGlobalTable](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_CreateGlobalTable.html)
+ [CreateTable](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_CreateTable.html)
+ [DeleteBackup](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DeleteBackup.html)
+ [DeleteTable](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DeleteTable.html)
+ [DescribeBackup](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DescribeBackup.html)
+ [DescribeContinuousBackups](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DescribeContinuousBackups.html)
+ [DescribeGlobalTable](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DescribeGlobalTable.html)
+ [DescribeLimits](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DescribeLimits.html)
+ [DescribeTable](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DescribeTable.html)
+ [DescribeTimeToLive](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DescribeTimeToLive.html)
+ [ListBackups](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_ListBackups.html)
+ [ListTables](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_ListTables.html)
+ [ListTagsOfResource](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_ListTagsOfResource.html)
+ [ListGlobalTables](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_ListGlobalTables.html)
+ [RestoreTableFromBackup](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_RestoreTableFromBackup.html)
+ [RestoreTableToPointInTime](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_RestoreTableToPointInTime.html)
+ [TagResource](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_TagResource.html)
+ [UntagResource](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_UntagResource.html)
+ [UpdateGlobalTable](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_UpdateGlobalTable.html)
+ [UpdateTable](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_UpdateTable.html)
+ [UpdateTimeToLive](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_UpdateTimeToLive.html)
+ DescribeReservedCapacity
+ DescribeReservedCapacityOfferings
+ DescribeScalableTargets
+ RegisterScalableTarget
+ PurchaseReservedCapacityOfferings

**DynamoDB Streams**
+ [DescribeStream](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_streams_DescribeStream.html)
+ [ListStreams](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_streams_ListStreams.html)

**DynamoDB Accelerator \(DAX\)**
+ [CreateCluster](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_CreateCluster.html)
+ [CreateParameterGroup](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_CreateParameterGroup.html)
+ [CreateSubnetGroup](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_CreateSubnetGroup.html)
+ [DecreaseReplicationFactor](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_DecreaseReplicationFactor.html)
+ [DeleteCluster](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_DeleteCluster.html)
+ [DeleteParameterGroup](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_DeleteParameterGroup.html)
+ [DeleteSubnetGroup](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_DeleteSubnetGroup.html)
+ [DescribeClusters](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_DescribeClusters.html)
+ [DescribeDefaultParameters](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_DescribeDefaultParameters.html)
+ [DescribeEvents](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_DescribeEvents.html)
+ [DescribeParameterGroups](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_DescribeParameterGroups.html)
+ [DescribeParameters](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_DescribeParameters.html)
+ [DescribeSubnetGroups](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_DescribeSubnetGroups.html)
+ [IncreaseReplicationFactor](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_IncreaseReplicationFactor.html)
+ [ListTags](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_ListTags.html)
+ [RebootNode](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_RebootNode.html)
+ [TagResource](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_TagResource.html)
+ [UntagResource](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_UntagResource.html)
+ [UpdateCluster](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_UpdateCluster.html)
+ [UpdateParameterGroup](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_UpdateParameterGroup.html)
+ [UpdateSubnetGroup](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_UpdateSubnetGroup.html)

### DynamoDB Data Plane Events in CloudTrail<a name="ddb-data-plane-events-in-cloudtrail"></a>

To enable logging of the following API actions in CloudTrail files, you'll need to enable logging of data plane API activity in CloudTrail\. See [Logging data events for trails](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/logging-data-events-with-cloudtrail.html) for more information\.

Data plane events can be filtered by resource type for granular control over which DynamoDB API calls you want to selectively log and pay for in CloudTrail\. For example, by specifying `AWS::DynamoDB::Stream` as a resource type, you can log only calls to the DynamoDB Streams APIs, enabling you to identify security issues while controlling costs\. For more information, see [DataResource](https://docs.aws.amazon.com/awscloudtrail/latest/APIReference/API_DataResource.html) in the CloudTrail [AWS CloudTrail API Reference](https://docs.aws.amazon.com/awscloudtrail/latest/APIReference/)\.

**Amazon DynamoDB**
+ [BatchExecuteStatement](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_BatchExecuteStatement.html)
+ [BatchGetItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_BatchGetItem.html)
+ [BatchWriteItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_BatchWriteItem.html)
+ [DeleteItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DeleteItem.html)
+ [ExecuteStatement](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_ExecuteStatement.html)
+ [ExecuteTransaction](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_ExecuteTransaction.html)
+ [GetItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_GetItem.html)
+ [PutItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_PutItem.html)
+ [Query](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Query.html)
+ [Scan](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Scan.html)
+ [TransactGetItems](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_TransactGetItems.html)
+ [TransactWriteItems](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_TransactWriteItems.html)
+ [UpdateItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_UpdateItem.html)

**Note**  
DynamoDB Time to Live data plane actions are not logged by CloudTrail

**DynamoDB Streams**
+ [GetRecords](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_streams_GetRecords.html)
+ [GetShardIterator](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_streams_GetShardIterator.html)