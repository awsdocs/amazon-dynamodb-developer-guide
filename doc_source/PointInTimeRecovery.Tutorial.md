# Restoring a DynamoDB Table to a Point in Time<a name="PointInTimeRecovery.Tutorial"></a>

Amazon DynamoDB point\-in\-time recovery \(PITR\) provides continuous backups of your DynamoDB table data\. You can restore a table to a point in time using the DynamoDB console or the AWS Command Line Interface \(AWS CLI\)\.

If you want to use the AWS CLI, you need to configure it first\. For more information, see [Accessing DynamoDB](AccessingDynamoDB.md)\.

**Topics**
+ [Restoring a Table to a Point in Time \(Console\)](#restoretabletopointintime_console)
+ [Restoring a Table to a Point in Time \(AWS CLI\)](#restorepointintime_cli)

## Restoring a Table to a Point in Time \(Console\)<a name="restoretabletopointintime_console"></a>

The following example demonstrates how to use the DynamoDB console to restore an existing table named `MusicCollection` to a point in time\. 
**Note**  
This procedure assumes that you have enabled point\-in\-time recovery\. To enable it for the `MusicCollection` table, on the **Overview** tab, in the **Table details** section, choose **Enable** for **Point\-in\-time recovery**\. 

1. Sign in to the AWS Management Console and open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. In the navigation pane on the left side of the console, choose **Tables**\.

1. In the list of tables, choose the `MusicCollection` table\. 

1. On the **Backups** tab of the `MusicCollection` table, in the **Point\-in\-time recovery** section, choose **Restore to point\-in\-time**\.

1. For the new table name, type **MusicCollectionMinutesAgo**\. 

1. To confirm the restorable time, set the **Restore date and time** to the **Latest restore date**\. Then choose **Restore table** to start the restore process\. 
**Note**  
You can restore to any point in time within **Earliest restore date** and **Latest restore date**\. DynamoDB restores your table data to the state based on the selected date and time \(day:hour:minute:second\)\. 

   The table that is being restored is shown with the status **Restoring**\. After the restore process is finished, the status of the `MusicCollectionMinutesAgo` table changes to **Active**\.

## Restoring a Table to a Point in Time \(AWS CLI\)<a name="restorepointintime_cli"></a>

The following procedure shows how to use the AWS CLI to restore an existing table named `MusicCollection` to a point in time\. 

**Note**  
This procedure assumes that you have enabled point\-in\-time recovery\. To enable it for the `MusicCollection` table, run the following command:   

```
aws dynamodb update-continuous-backups \
--table-name MusicCollection \
--point-in-time-recovery-specification PointInTimeRecoveryEnabled=True
```

1. Confirm that point\-in\-time recovery is enabled for the `MusicCollection` table by using the `describe-continuous-backups` command\. 

   ```
   aws dynamodb describe-continuous-backups \
   --table-name MusicCollection
   ```

   Continuous backups \(automatically enabled on table creation\) and point\-in\-time recovery are enabled\. 

   ```
   {
       "ContinuousBackupsDescription": {
           "PointInTimeRecoveryDescription": {
               "PointInTimeRecoveryStatus": "ENABLED", 
               "EarliestRestorableDateTime": 1519257118.0, 
               "LatestRestorableDateTime": 1520018653.01
           }, 
           "ContinuousBackupsStatus": "ENABLED"
       }
   }
   ```

1. Restore the table to a point in time\. In this case, the `MusicCollection` table is restored to the `LatestRestorableDateTime` \(\~5 minutes ago\)\. 

   ```
   aws dynamodb restore-table-to-point-in-time \
   --source-table-name MusicCollection \
   --target-table-name MusicCollectionMinutesAgo \
   --use-latest-restorable-time
   ```
**Note**  
 You can also restore to a specific point in time\. To do this, run the command using the `--restore-date-time` argument, and specify a time stamp\. You can specify any point in time during the last 35 days\. For example, the following command restores the table to the `EarliestRestorableDateTime`\.   

   ```
   aws dynamodb restore-table-to-point-in-time \
   --source-table-name MusicCollection \
   --target-table-name MusicCollectionEarliestRestorableDateTime \
   --no-use-latest-restorable-time \
   --restore-date-time 1519257118.0
   ```
 Specifying the `--no-use-latest-restorable-time` argument is optional when restoring to a specific point in time\. 

To verify the restore, use the `describe-table` command to describe the `MusicCollection` table: 

```
aws dynamodb describe-table --table-name MusicCollection 
```

The table that is being restored is shown with the status **Creating** and restore in progress as **true**\. After the restore process is finished, the status of the `MusicCollection` table changes to **Active**\.

**Important**  
 While a restore is in progress, don't modify or delete the AWS Identity and Access Management \(IAM\) policies that grant the IAM entity \(for example, user, group, or role\) permission to perform the restore\. Otherwise, unexpected behavior can result\. For example, suppose that you removed write permissions for a table while that table was being restored\. In this case, the underlying `RestoreTableToPointInTime` operation can't write any of the restored data to the table\. Note that IAM policies involving source IP restrictions for accessing the target restore table may similarly cause issues\.   
You can modify or delete permissions only after the restore operation is completed\.
