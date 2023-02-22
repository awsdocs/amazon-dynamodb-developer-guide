# Monitoring global tables<a name="V2globaltables_monitoring"></a>


****  

|  | 
| --- |
| There are two versions of DynamoDB global tables available: [Version 2019\.11\.21 \(Current\)](globaltables.V2.md) and [Version 2017\.11\.29 \(Legacy\)](globaltables.V1.md)\. Customers should use version 2019\.11\.21 \(Current\) when possible, as it provides greater flexibility, higher efficiency and consumes less write capacity than 2017\.11\.29 \(Legacy\)\. To determine which version you are using, see [Determining the global table version you are using](globaltables.DetermineVersion.md)\. | 

You can use Amazon CloudWatch to monitor the behavior and performance of a global table\. Amazon DynamoDB publishes `ReplicationLatency` metric for each replica in the global table\.
+  **`ReplicationLatency`**—The elapsed time between when an item is written to a replica table, and when that item appears in another replica in the global table\. `ReplicationLatency` is expressed in milliseconds and is emitted for every source\- and destination\-Region pair\.

  During normal operation, `ReplicationLatency` should be fairly constant\. An elevated value for `ReplicationLatency` could indicate that updates from one replica are not propagating to other replica tables in a timely manner\. Over time, this could result in other replica tables *falling behind* because they no longer receive updates consistently\. In this case, you should verify that the read capacity units \(RCUs\) and write capacity units \(WCUs\) are identical for each of the replica tables\. In addition, when choosing WCU settings, follow the recommendations in [Global tables versionRequirements for Managing Capacity](V2globaltables_reqs_bestpractices.md#V2globaltables_reqs_bestpractices.tables)\.

  `ReplicationLatency` can increase if an AWS Region becomes degraded and you have a replica table in that Region\. In this case, you can temporarily redirect your application's read and write activity to a different AWS Region\.

 For more information, see [DynamoDB Metrics and dimensions](metrics-dimensions.md)\. 