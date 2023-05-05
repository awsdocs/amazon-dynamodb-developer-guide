# Determining the global table version you are using<a name="globaltables.DetermineVersion"></a>

There are two versions of DynamoDB global tables available: [Version 2019\.11\.21 \(Current\)](globaltables.V2.md) and [Version 2017\.11\.29 \(Legacy\)](globaltables.V1.md)\. We recommend using [Version 2019\.11\.21 \(Current\)](globaltables.V2.md) \. It is more efficient and consumes less write capacity than [Version 2017\.11\.29 \(Legacy\)](globaltables.V1.md)\. The advantages of the current version include:
+ The source and target tables are maintained together and kept aligned automatically for throughput, TTL settings, auto\-scaling settings, and other useful attributes\.
+ Global secondary indexes are also kept aligned\.
+ You can dynamically add new replica tables from a table populated with data
+ The metadata attributes required to control replication are hidden which helps prevent writing of them which would cause issues with replication\.
+ The current version supports more Regions than the legacy version, and lets you add or remove Regions to an existing table while the legacy version does not\.
+  [Version 2019\.11\.21 \(Current\)](globaltables.V2.md) is more efficient and consumes less write capacity than [Version 2017\.11\.29 \(Legacy\)](globaltables.V1.md), and therefore is more cost effective\. In specifics:
  + Inserting a new item in one Region and then replicating to other Regions requires 2 rWCUs per region for Version 2017\.11\.29 \(Legacy\), but only 1 for Version 2019\.11\.21 \(Current\)\. 
  + Updating an item requires 2 rWCUs in the source Region and 1 then rWCU per destination Region in Version 2017\.11\.29 \(Legacy\), but only 1 rWCU per source or destination in Version 2019\.11\.21 \(Current\)\.
  + Deleting an item requires 1 rWCU in the source Region and then 2 rWCUs per destination Region in Version 2017\.11\.29 \(Legacy\), but only 1 rWCU per source or destination in Version 2019\.11\.21 \(Current\)\.

  For more information see [Amazon DynamoDB Pricing](https://aws.amazon.com/dynamodb/pricing)\.

## Determing the version through the CLI<a name="globaltables.CLI"></a>

To find out which version of global tables you are usingthrough the AWS CLI, check `DescribeTable` and `DescribeGlobalTable`\. `DescribeGlobalTable` will show the table version if it is Version 2019\.11\.21 \(Current\), and `DescribeGlobalTable` property will show the table version if it is Version 2017\.11\.29 \(Legacy\)\.

## Determing the version through the console<a name="globaltables.console"></a>

Finding the version through the console

To find out which version of global tables you are using through the console, do the following:

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/home](https://console.aws.amazon.com/dynamodb/home)\.

1. In the navigation pane on the left side of the console, choose **Tables**\.

1. Choose the table you want to use\.

1. Choose the **Global Tables** tab\.

1. The **Global table version** displays the version of global tables in use:

![\[\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/GlobalTables-determineversion.png)

To upgrade from global tables Version 2017\.11\.29 \(Legacy\) to Version 2019\.11\.21 \(Current\), follow these steps [here](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/V2globaltables_upgrade.html#V2globaltables_upgrade.upgrade)\. The overall upgrade process will work without disrupting live tables, and should finish in less than an hour\. For more information, see [ Updating to Version 2019\.11\.21 \(Current\)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/V2globaltables_upgrade.html)

**Note**  
If you are using [Version 2019\.11\.21 \(Current\)](globaltables.V2.md) of global tables and you also use the [Time to Live](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/TTL.html) feature, DynamoDB replicates TTL deletes to all replica tables\. The initial TTL delete does not consume write capacity in the region in which the TTL expiry occurs\. However, the replicated TTL delete to the replica table\(s\) consumes a replicated write capacity unit when using provisioned capacity, or replicated write when using on\-demand capacity mode, in each of the replica regions and applicable charges will apply\. 
In [Version 2019\.11\.21 \(Current\)](globaltables.V2.md), when a TTL delete occurs it is replicated to all replica regions\. These replicated writes do not contain `type` or `principalID` properties\. This can make it difficult to distinguish a TTL delete from a user delete in the replicated tables\. 