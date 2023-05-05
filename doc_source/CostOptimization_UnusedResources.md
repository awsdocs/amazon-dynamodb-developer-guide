# Identifying your unused resources<a name="CostOptimization_UnusedResources"></a>

This section provides an overview of how to evaluate your unused resources regularly\. As your application requirements evolve you should ensure no resources are unused and contributing to unnecessary Amazon DynamoDB costs\. The procedures described below will use Amazon CloudWatch metrics to identify unused resources and will help you identify and take action on those resources to reduce costs\.

You can monitor DynamoDB using CloudWatch, which collects and processes raw data from DynamoDB into readable, near real\-time metrics\. These statistics are retained for a period of time, so that you can access historical information to better understand your utilization\. By default, DynamoDB metric data is sent to CloudWatch automatically\. For more information, see [What is Amazon CloudWatch?](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html) and [Metrics retention](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch_concepts.html#metrics-retention) in the *Amazon CloudWatch User Guide*\. 

**Topics**
+ [How to identify unused resources](#CostOptimization_UnusedResources_Identifying)
+ [Identifying unused table resources](#CostOptimization_UnusedResources_Tables)
+ [Cleaning up unused table resources](#CostOptimization_UnusedResources_Tables_Cleanup)
+ [Identifying unused GSI resources](#CostOptimization_UnusedResources_GSI)
+ [Cleaning up unused GSI resources](#CostOptimization_UnusedResources_GSI_Cleanup)
+ [Cleaning up unused global tables](#CostOptimization_UnusedResources_GlobalTables)
+ [Cleaning up unused backups or point\-in\-time recovery \(PITR\)](#CostOptimization_UnusedResources_Backups)

## How to identify unused resources<a name="CostOptimization_UnusedResources_Identifying"></a>

To identify unused tables or indexes, we'll look at the following CloudWatch metrics over a period of 30 days to understand if there are any active reads or writes on the table or any reads on the global secondary indexes \(GSIs\):

**[ConsumedReadCapacityUnits](metrics-dimensions.md#ConsumedReadCapacityUnits)**  
The number of read capacity units consumed over the specified time period, so you can track how much consumed capacity you have used\. You can retrieve the total consumed read capacity for a table and all of its global secondary indexes, or for a particular global secondary index\.

**[ConsumedWriteCapacityUnits](metrics-dimensions.md#ConsumedWriteCapacityUnits)**  
The number of write capacity units consumed over the specified time period, so you can track how much consumed capacity you have used\. You can retrieve the total consumed write capacity for a table and all of its global secondary indexes, or for a particular global secondary index\.

## Identifying unused table resources<a name="CostOptimization_UnusedResources_Tables"></a>

Amazon CloudWatch is a monitoring and observability service which provides the DynamoDB table metrics you’ll use to identify unused resources\. CloudWatch metrics can be viewed through the AWS Management Console as well as through the AWS Command Line Interface\.

------
#### [ AWS Command Line Interface ]

To view your tables metrics through the AWS Command Line Interface, you can use the following commands\.

1. First, evaluate your table's reads:

   ```
   aws cloudwatch get-metric-statistics --metric-name
   ConsumedReadCapacityUnits --start-time <start-time> --end-time <end-
   time> --period <period> --namespace AWS/DynamoDB --statistics Sum --
   dimensions Name=TableName,Value=<table-name>
   ```

   To avoid falsely identifying a table as unused, evaluate metrics over a longer period\. Choose an appropriate start\-time and end\-time range, such as **30 days**, and an appropriate period, such as **86400**\.

   In the returned data, any **Sum** above **0** indicates that the table you are evaluating received read traffic during that period\.

   The following result shows a table receiving read traffic in the evaluated period:

   ```
           {
               "Timestamp": "2022-08-25T19:40:00Z",
               "Sum": 36023355.0,
               "Unit": "Count"
           },
           {
               "Timestamp": "2022-08-12T19:40:00Z",
               "Sum": 38025777.5,
               "Unit": "Count"
           },
   ```

   The following result shows a table not receiving read traffic in the evaluated period:

   ```
           {
               "Timestamp": "2022-08-01T19:50:00Z",
               "Sum": 0.0,
               "Unit": "Count"
           },
           {
               "Timestamp": "2022-08-20T19:50:00Z",
               "Sum": 0.0,
               "Unit": "Count"
           },
   ```

1. Next, evaluate your table’s writes:

   ```
   aws cloudwatch get-metric-statistics --metric-name
   ConsumedWriteCapacityUnits --start-time <start-time> --end-time <end-
   time> --period <period> --namespace AWS/DynamoDB --statistics Sum --
   dimensions Name=TableName,Value=<table-name>
   ```

   To avoid falsely identifying a table as unused, you will want to evaluate metrics over a longer period\. Choose an appropriate start\-time and end\-time range, such as **30 days**, and an appropriate period, such as **86400**\.

   In the returned data, any **Sum** above **0** indicates that the table you are evaluating received read traffic during that period\.

   The following result shows a table receiving write traffic in the evaluated period:

   ```
           {
               "Timestamp": "2022-08-19T20:15:00Z",
               "Sum": 41014457.0,
               "Unit": "Count"
           },
           {
               "Timestamp": "2022-08-18T20:15:00Z",
               "Sum": 40048531.0,
               "Unit": "Count"
           },
   ```

   The following result shows a table not receiving write traffic in the evaluated period:

   ```
           {
               "Timestamp": "2022-07-31T20:15:00Z",
               "Sum": 0.0,
               "Unit": "Count"
           },
           {
               "Timestamp": "2022-08-19T20:15:00Z",
               "Sum": 0.0,
               "Unit": "Count"
           },
   ```

------
#### [ AWS Management Console ]

The following steps will allow you to evaluate your resources utilization through the AWS Management Console\.

1. Log into the AWS console and navigate to the CloudWatch service page at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\. Select the appropriate AWS region in the top right of the console, if necessary\.

1. On the left navigation bar, locate the Metrics section and select **All metrics**\.

1. The action above will open a dashboard with two panels\. In the top panel you will see currently graphed metrics\. In the bottom you will select the metrics available to graph\. Select DynamoDB in the bottom panel\.

1. In the DynamoDB metrics selection panel select the **Table Metrics** category to show the metrics for your tables in the current region\.

1. Identify your table name by scrolling down the menu, then select the metrics `ConsumedReadCapacityUnits` and `ConsumedWriteCapacityUnits` for your table\.

1. Select the **Graphed metrics \(2\)** tab and adjust the **Statistic** column to **Sum**\.  
![\[Image showing the Graphed metrics tab from the AWS Management Console.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/CostOptimization/GraphedMetricsTab.png)

1. To avoid falsely identifying a table as unused, you'll want to evaluate metrics over a longer period\. At the top of the graph panel choose an appropriate time frame, such as 1 month, to evaluate your table\. Select **Custom**, select **1 Months** in the dropdowns, and choose **Apply**\.  
![\[Image showing a one month time frame in the AWS Management Console.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/CostOptimization/OneMonthTimeFrame.png)

1. Evaluate the graphed metrics for your table to determine if it is being used\. Metrics that have gone above **0 **indicate that a table has been used during the evaluated time period\. A flat graph at **0 **for both read and write indicates a table that is unused\.

   The following image shows a table with read traffic:  
![\[Image showing a table with read traffic in the AWS Management Console.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/CostOptimization/TableWithReadTraffic.png)

   The following image shows a table without read traffic:  
![\[Image showing a table without read traffic AWS Management Console.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/CostOptimization/TableWithoutReadTraffic.png)

------

## Cleaning up unused table resources<a name="CostOptimization_UnusedResources_Tables_Cleanup"></a>

If you have identified unused table resources, you can reduce their ongoing costs in the following ways\.

**Note**  
If you have identified an unused table but would still like to keep it available in case it needs to be accessed in the future, consider switching it to on\-demand mode\. Otherwise, you can consider backing up and deleting the table entirely\.

**Capacity modes**  
DynamoDB charges for reading, writing, and storing data in your DynamoDB tables\.

DynamoDB has [two capacity modes](HowItWorks.ReadWriteCapacityMode.md), which come with specific billing options for processing reads and writes on your tables: on\-demand and provisioned\. The read/write capacity mode controls how you are charged for read and write throughput and how you manage capacity\.

For on\-demand mode tables, you don't need to specify how much read and write throughput you expect your application to perform\. DynamoDB charges you for the reads and writes that your application performs on your tables in terms of read request units and write request units\. If there is no activity on your table/index you do not pay for throughput but you’ll still incur a storage charge\.

**Table class**  
DynamoDB also offers [two table classes](HowItWorks.TableClasses.md) designed to help you optimize for cost\. The DynamoDB Standard table class is the default and is recommended for most workloads\. The DynamoDB Standard\-Infrequent Access \(DynamoDB Standard\-IA\) table class is optimized for tables where storage is the dominant cost\.

If there is no activity on your table or index, storage is likely to be the dominant cost and changing table class will offer a significant savings\.

**Deleting tables**  
If you’ve discovered an unused table and would like to delete it, you may wish to make a backup or export of the data first\.

Backups taken through AWS Backup can leverage cold storage tiering, further reducing costs\. Refer to the [Using AWS Backup with DynamoDB](backuprestore_HowItWorksAWS.md) documentation for information on how enable backups through AWS Backup as well as the [Managing backup plans](https://docs.aws.amazon.com/aws-backup/latest/devguide/about-backup-plans) documentation for information on how to use lifecycle to move your backup to cold storage\.

Alternatively, you may choose to export your table’s data to S3\. To do so, refer to the [Export to Amazon S3](S3DataExport.HowItWorks.md) documentation\. Once your data is exported, if you wish to leverage S3 Glacier Instant Retrieval, S3 Glacier Flexile Retrieval, or S3 Glacier Deep Archive to further reduce costs, see [Managing your storage lifecycle](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lifecycle-mgmt)\.

After your table has been backed up, you may choose to delete it either through the AWS Management Console or through the AWS Command Line Interface\.

## Identifying unused GSI resources<a name="CostOptimization_UnusedResources_GSI"></a>

The steps for identifying an unused global secondary are similar to those for identifying an unused table\. Since DynamoDB replicates items written to your base table into your GSI if they contain the attribute used as the GSI’s partition key, an unused GSI is still likely to have `ConsumedWriteCapacityUnits` above 0 if its base table is in use\. As a result, you’ll be evaluating only the `ConsumedReadCapacityUnits` metric to determine if your GSI is unused\.

To view your GSI metrics through the AWS AWS CLI, you can use the following commands to evaluate your tables reads:

```
aws cloudwatch get-metric-statistics --metric-name
ConsumedReadCapacityUnits --start-time <start-time> --end-time <end-
time> --period <period> --namespace AWS/DynamoDB --statistics Sum --
dimensions Name=TableName,Value=<table-name>
Name=GlobalSecondaryIndexName,Value=<index-name>
```

To avoid falsely identifying a table as unused, you will want to evaluate metrics over a longer period\. Choose an appropriate start\-time and end\-time range, such as 30 days, and an appropriate period, such as 86400\.

In the returned data, any Sum above 0 indicates that the table you are evaluating received read traffic during that period\.

The following result shows a GSI receiving read traffic in the evaluated period:

```
        {
          "Timestamp": "2022-08-17T21:20:00Z",
          "Sum": 36319167.0,
          "Unit": "Count"
        },
        {
          "Timestamp": "2022-08-11T21:20:00Z",
          "Sum": 1869136.0,
          "Unit": "Count"
        },
```

The following result shows a GSI receiving minimal read traffic in the evaluated period:

```
        {
          "Timestamp": "2022-08-28T21:20:00Z",
          "Sum": 0.0,
          "Unit": "Count"
        },
        {
          "Timestamp": "2022-08-15T21:20:00Z",
          "Sum": 2.0,
          "Unit": "Count"
        },
```

The following result shows a GSI receiving no read traffic in the evaluated period:

```
        {
          "Timestamp": "2022-08-17T21:20:00Z",
          "Sum": 0.0,
          "Unit": "Count"
        },
        {
          "Timestamp": "2022-08-11T21:20:00Z",
          "Sum": 0.0,
          "Unit": "Count"
        },
```

## Cleaning up unused GSI resources<a name="CostOptimization_UnusedResources_GSI_Cleanup"></a>

If you've identified an unused GSI, you can choose to delete it\. Since all data present in a GSI is also present in the base table, additional backup is not necessary before deleting a GSI\. If in the future the GSI is once again needed, it may be added back to the table\.

If you have identified an infrequently used GSI, you should consider design changes in your application that would allow you to delete it or reduce its cost\. For example, while DynamoDB scans should be used sparingly because they can consume large amounts of system resources, they may be more cost effective than a GSI if the access pattern it supports is used very infrequently\.

Additionally, if a GSI is required to support an infrequent access pattern consider projecting a more limited set of attributes\. While this may require subsequent queries against the base table to support your infrequent access patterns, it can potentially offer a significant reduction in storage and write costs\.

## Cleaning up unused global tables<a name="CostOptimization_UnusedResources_GlobalTables"></a>

Amazon DynamoDB global tables provide a fully managed solution for deploying a multi\-Region, multi\-active database, without having to build and maintain your own replication solution\.

Global tables are ideal for providing low\-latency access to data close to users and as well as a secondary region for disaster recovery\.

If the global tables option is enabled for a resource in an effort to provide low\-latency access to data but is not part of your disaster recovery strategy, validate that both replicas are actively serving read traffic by evaluating their CloudWatch metrics\. If one replica does not serve read traffic, it may be an unused resource\.

If global tables are part of your disaster recovery strategy, one replica not receiving read traffic may be expected under an active/standby pattern\.

## Cleaning up unused backups or point\-in\-time recovery \(PITR\)<a name="CostOptimization_UnusedResources_Backups"></a>

DynamoDB offers two styles of backup\. Point\-in\-time recovery provides continuous backups for 35 days to help you protect against accidental writes or deletes while on\-demand backup allows for snapshot creation which can be saved long term\. Both types of backups have costs associated with them\.

Refer to the documentation for [Using On\-Demand backup and restore for DynamoDB](BackupRestore.md) and [Point\-in\-time recovery for DynamoDB](PointInTimeRecovery.md) to determine if your tables have backups enabled that may no longer be needed\.