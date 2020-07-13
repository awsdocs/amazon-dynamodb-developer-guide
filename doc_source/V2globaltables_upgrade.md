# Updating Global Tables to Version 2019\.11\.21 \(Current\)<a name="V2globaltables_upgrade"></a>


****  

|  | 
| --- |
| There are two versions of DynamoDB global tables available: [Version 2019\.11\.21 \(Current\)](globaltables.V2.md) and [Version 2017\.11\.29](globaltables.V1.md)\. To find out which version you are using, see [Determine Version](globaltables.DetermineVersion.md)\. | 

This section describes how to update your global tables to *version 2019\.11\.21* \(current\)\.

**Topics**
+ [Before You Begin](#V2globaltables_upgrade.Notes)
+ [Updating to Version 2019\.11\.21 \(Current\)](#V2globaltables_upgrade.upgrade)

## Before You Begin<a name="V2globaltables_upgrade.Notes"></a>

DynamoDB global tables version 2019\.11\.21 \(current\) introduces the following requirements:
+ The [global secondary indexes](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GSI.html) on the replica tables must be consistent across Regions\.
+ The [encryption](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/EncryptionAtRest.html) setting on the replica tables must be consistent across Regions\.
+ The [Time To Live \(TTL\)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/TTL.html) settings on the replica tables must be consistent across Regions\. If TTL is enabled on a replica table, TTL deletes are replicated\.
+ DynamoDB auto scaling or [on\-demand](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.ReadWriteCapacityMode.html#HowItWorks.OnDemand) capacity must be enabled for write capacity units across all replica tables\.
+ Global tables control plane operations \(create, delete, update, and describe\) APIs differ\. For more information, see [Global Tables: Multi\-Region Replication with DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GlobalTables.html)\.

DynamoDB global tables version 2019\.11\.21 \(current\) introduces the following changes in behavior:
+ DynamoDB Streams publishes only one record \(instead of two\) for each write\.
+ For new inserts, `aws:rep:*` attributes in the item record are not added\.
+ For updates to items that contain `aws:rep:*` attributes, those attributes are not updated\.
+ DynamoDB mapper must not require the `aws:rep:*` attributes for this table\.
+ When updating from version 2017\.11\.21 to version 2019\.11\.29, you might see an increase in the `ReplicationLatency` metric\. This is expected because version 2019\.11\.29 \(current\) captures the full end\-to\-end measurement of replication delay between global tables Regions\. For more information, see the `ReplicationLatency` documentation for version [Version 2017\.11\.21](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/globaltables_monitoring.html) and [Version 2019\.11\.29 \(Current\)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/V2globaltables_monitoring.html)\.

### Required Permissions<a name="V2globaltables_upgrade.Notes-permissions"></a>

To update to version 2019\.11\.21 \(current\), you must have `dynamodb:UpdateGlobalTableVersion` permissions across replica Regions\. These permissions are in addition to the permissions needed for accessing the DynamoDB console and viewing tables\.

The following IAM policy grants permissions to update any global table to version 2019\.11\.21 \(current\)\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "dynamodb:UpdateGlobalTableVersion",
            "Resource": "*"
        }
    ]
}
```

The following IAM policy grants permissions to update only the `Music` global table, with replicas in two Regions, to version 2019\.11\.21 \(current\)\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "dynamodb:UpdateGlobalTableVersion",
            "Resource": [
                "arn:aws:dynamodb::123456789012:global-table/Music",
                "arn:aws:dynamodb:ap-southeast-1:123456789012:table/Music",
                "arn:aws:dynamodb:us-east-2:123456789012:table/Music"
            ]
        }
    ]
}
```

### Update Process Overview<a name="V2globaltables_upgrade.Notes-process"></a>

During the update process, the state of your global table changes from **ACTIVE** to **UPDATING**\.

The process takes several minutes, but it should finish in less than an hour\. During the update process, your table remains available for read and write traffic\. However, auto scaling does not alter the provisioned capacity on your table during the update\. As such, before you begin the update, we recommend that you switch your table to [on\-demand](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.ReadWriteCapacityMode.html#HowItWorks.OnDemand) capacity\. 

If you choose to use [provisioned](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.ReadWriteCapacityMode.html#HowItWorks.ProvisionedThroughput.Manual) capacity with auto scaling during the update, you must increase the minimum read and write throughput on your policies to accommodate expected increases in traffic for the duration of the update\.

When the update process is complete, your table status returns to **ACTIVE**\. You can check the state of your table by using `DescribeTable`, or verify it using the **Tables** view on the DynamoDB console\.

**Important**  
Updating from version 2017\.11\.29 to version 2019\.11\.21 \(current\) is a one\-time action and cannot be reversed\. Before proceeding with the update, ensure that you have performed all necessary testing\. Allow up to 60 minutes before attempting to update a newly created global table\. 
In version 2017\.11\.29 of global tables, DynamoDB performed a write operation to insert three attributes into the item record\. These attributes \(`aws:rep:*`\) were used to enact replication and manage conflict resolution\. In version 2019\.11\.21 \(current\) of global tables, replication activity is managed natively and is not exposed to users\. 
Updating to version 2019\.11\.21 \(current\) is only available through the DynamoDB console\.

## Updating to Version 2019\.11\.21 \(Current\)<a name="V2globaltables_upgrade.upgrade"></a>

Follow these steps to update your version of DynamoDB global tables using the AWS Management Console\.

**To update global tables to version 2019\.11\.21**

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/home](https://console.aws.amazon.com/dynamodb/home)\. 

1. In the navigation pane on the left side of the console, choose **Tables**, and then select the global table that you want to update to 2019\.11\.21 \(current\)\.

1. Choose the **Global Tables** tab\.

1. Choose **Update version**\.  
![\[Console screenshot showing the Update version button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/GlobalTables-upgrade.png)

1. Read and agree to the new requirements, and then choose **Continue**\.
**Important**  
Updating from version 2017\.11\.29 to version 2019\.11\.21 \(current\) is a one\-time action and cannot be reversed\. Before starting the update, ensure that you have performed all necessary testing\.  
![\[Console screenshot showing the Update dialog box containing compatibility requirements.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/GlobalTables-upgrade-execute.png)

1. After the update process is complete, the global tables version that appears on the console changes to **2019\.11\.21**\.  
![\[Console screenshot showing the current global table version number.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/GlobalTables-determineversion.png)