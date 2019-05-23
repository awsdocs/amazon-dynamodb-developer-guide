# Monitoring Global Tables<a name="globaltables_monitoring"></a>

You can use Amazon CloudWatch to monitor the behavior and performance of a global table\. Amazon DynamoDB publishes `ReplicationLatency` and `PendingReplicationCount` metrics for each replica in the global table\.
+  **`ReplicationLatency`**—the elapsed time between an updated item appearing in the DynamoDB stream for one replica table, and that item appearing in another replica in the global table\. `ReplicationLatency` is expressed in milliseconds, and is emitted for every source\- and destination\-region pair\.

  During normal operation, `ReplicationLatency` should be fairly constant\. An elevated value for `ReplicationLatency` could indicate that updates from one replica are not propagating to other replica tables in a timely manner\. Over time, this could result in other replica tables "falling behind", as they no longer receive updates consistently\. In this case, you should verify that the read capacity units \(RCUs\) and write capacity units \(WCUs\) are identical for each of the replica tables\. In addition, the WCU settings you choose should follow the recommendations in [Best Practices and Requirements for Managing Capacity](globaltables_reqs_bestpractices.md#globaltables_reqs_bestpractices.tables)\.

  `ReplicationLatency` can increase if an AWS region becomes degraded, and you have a replica table in that region\. In this case, you can temporarily redirect your application's read and write activity to a different AWS region\.
+ ** `PendingReplicationCount`**—the number of item updates that are written to one replica table, but that have not yet been written to another replica in the global table\. `PendingReplicationCount` is expressed in number of items, and is emitted for every source\- and destination\-region pair\.

  During normal operation, `PendingReplicationCount` should be very low\. If `PendingReplicationCount` increases for extended periods, you should investigate whether your replica tables' provisioned write capacity settings are sufficient for your current workload\.

  `PendingReplicationCount` can increase if an AWS region becomes degraded, and you have a replica table in that region\. In this case, you can temporarily redirect your application's read and write activity to a different AWS region\.

 For more information, see [DynamoDB Metrics and Dimensions](metrics-dimensions.md)\. 