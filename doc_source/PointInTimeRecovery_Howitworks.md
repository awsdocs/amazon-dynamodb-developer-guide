# Point\-in\-Time Recovery: How It Works<a name="PointInTimeRecovery_Howitworks"></a>

Amazon DynamoDB point\-in\-time recovery \(PITR\) provides automatic backups of your DynamoDB table data\. This section provides an overview of how the process works in DynamoDB\.

 You can enable point\-in\-time recovery using the AWS Management Console, AWS Command Line Interface \(AWS CLI\), or the DynamoDB API\. When it's enabled, point\-in\-time recovery provides continuous backups until you explicitly turn it off\. For more information, see [Restoring a DynamoDB Table to a Point in Time](PointInTimeRecovery.Tutorial.md)\.

After you enable point\-in\-time recovery, you can restore to any point in time within `EarliestRestorableDateTime` and `LatestRestorableDateTime`\. `LatestRestorableDateTime` is typically 5 minutes before the current time\. 

**Note**  
The point\-in\-time recovery process always restores to a new table\. 

For `EarliestRestorableDateTime`, you can restore your table to any point in time during the last 35 days\. The retention period is a fixed 35 days \(five calendar weeks\) and can't be modified\. Any number of users can execute up to four concurrent restores \(any type of restore\) in a given account\. 

**Important**  
 If you disable point\-in\-time recovery and later re\-enable it on a table, you reset the start time for which you can recover that table\. As a result, you can only immediately restore that table using the `LatestRestorableDateTime`\. 

 When you restore using point in time recovery, DynamoDB restores your table data to the state based on the selected date and time \(day:hour:minute:second\) to a new table\. 

 Along with data, the following are also included on the new restored table using point in time recovery: 
+ Global secondary indexes \(GSIs\)
+ Local secondary indexes \(LSIs\)
+ Provisioned read and write capacity
+ Encryption settings
**Important**  
 All these settings come from the current settings of the source table at the time of restore and will be applied to the new restored table\. For example, let's say a table's provisioned throughput was just lowered to 50 RCUs and 50 WCUs\. If you restore this table's state to 3 weeks ago, which had its provisioned throughput set at that time at 100 RCUs and 100WCUs, DynamoDB restores your table data to that point in time but utilizes the current table's provisioned throughput at the time of restore \(50 RCUs and 50 WCUs\)\. 

After restoring a table, you must manually set up the following on the restored table:
+ Auto scaling policies
+ AWS Identity and Access Management \(IAM\) policies
+ Amazon CloudWatch metrics and alarms
+ Tags
+ Stream settings
+ Time to Live \(TTL\) settings
+ Point\-in\-time recovery settings

 The time it takes you to restore a table will vary based on multiple factors, and the point in time restore times are not always correlated directly to the size of the table\. For more information, see [Restores](backuprestore_HowItWorks.md#backuprestore_HowItWorks-restore)\. 