# Before You Begin Using Point In Time Recovery<a name="pointintimerecovery_beforeyoubegin"></a>

Before you enable point\-in\-time recovery \(PITR\) on an Amazon DynamoDB table, consider the following:
+  If you disable point\-in\-time recovery and later re\-enable it on a table, you reset the start time for which you can recover that table\. As a result, you can only immediately restore that table using the `LatestRestorableDateTime`\. 
+  If you need to recover a deleted table that had point in time recovery enabled, you need to contact AWS Support to restore that table within the 35\-day recovery window: [https://aws\.amazon\.com/support](https://aws.amazon.com/support)\. 
+ You can enable point\-in\-time recovery on each local replica of a global table\. When you restore the table, the backup restores to an independent table that is not part of the global table\. For more information, see [Global Tables: How It Works](globaltables_HowItWorks.md)\.
+ You can enable point\-in\-time recovery on an encrypted table\.
+ AWS CloudTrail logs all console and API actions for point\-in\-time recovery to enable logging, continuous monitoring, and auditing\. For more information, see [Logging DynamoDB Operations by Using AWS CloudTrail](logging-using-cloudtrail.md)\. 