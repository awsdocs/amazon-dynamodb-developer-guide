# Restoring a DynamoDB table to a point in time<a name="PointInTimeRecovery.Tutorial"></a>

Amazon DynamoDB point\-in\-time recovery \(PITR\) provides continuous backups of your DynamoDB table data\. You can restore a table to a point in time using the DynamoDB console or the AWS Command Line Interface \(AWS CLI\)\. The point\-in\-time recovery process restores to a new table\.

If you want to use the AWS CLI, you must configure it first\. For more information, see [Accessing DynamoDB](AccessingDynamoDB.md)\.

**Topics**
+ [Restoring a DynamoDB table to a point in time \(console\)](#restoretabletopointintime_console)
+ [Restoring a table to a point in time \(AWS CLI\)](#restorepointintime_cli)

## Restoring a DynamoDB table to a point in time \(console\)<a name="restoretabletopointintime_console"></a>

The following example demonstrates how to use the DynamoDB console to restore an existing table named `Music` to a point in time\.

**Note**  
This procedure assumes that you have enabled point\-in\-time recovery\. To enable it for the `Music` table, on the **Backups** tab, in the **Point\-in\-time recovery \(PITR\)** section, choose **Edit** and then check the box next to **Enable point\-in\-time\-recovery**\.

**To restore a table to a point in time**

1. Sign in to the AWS Management Console and open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. In the navigation pane on the left side of the console, choose **Tables**\.

1. In the list of tables, choose the `Music` table\.

1. On the **Backups** tab of the `Music` table, in the **Point\-in\-time recovery \(PITR\)** section, choose **Restore**\.

1. For the new table name, enter **MusicMinutesAgo**\.
**Note**  
You can restore the table to the same AWS Region or to a different Region from where the source table resides\. You can also exclude secondary indexes from being created on the restored table\. In addition, you can specify a different encryption mode\.

1. To confirm the restorable time, set the restore date and time to **Earliest**\. Then choose **Restore** to start the restore process\.

   The table that is being restored is shown with the status **Restoring**\. After the restore process is finished, the status of the `MusicMinutesAgo` table changes to **Active**\.

## Restoring a table to a point in time \(AWS CLI\)<a name="restorepointintime_cli"></a>

The following procedure shows how to use the AWS CLI to restore an existing table named `Music` to a point in time\.

**Note**  
This procedure assumes that you have enabled point\-in\-time recovery\. To enable it for the `Music` table, run the following command\.  

```
aws dynamodb update-continuous-backups \
    --table-name Music \
    --point-in-time-recovery-specification PointInTimeRecoveryEnabled=True
```



**To restore a table to a point in time**

1. Confirm that point\-in\-time recovery is enabled for the `Music` table by using the `describe-continuous-backups` command\.

   ```
   aws dynamodb describe-continuous-backups \
       --table-name Music
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

1. Restore the table to a point in time\. In this case, the `Music` table is restored to the `LatestRestorableDateTime` \(\~5 minutes ago\) to the same AWS Region\.

   ```
   aws dynamodb restore-table-to-point-in-time \
       --source-table-name Music \
       --target-table-name MusicMinutesAgo \
       --use-latest-restorable-time
   ```
**Note**  
 You can also restore to a specific point in time\. To do this, run the command using the `--restore-date-time` argument, and specify a timestamp\. You can specify any point in time during the last 35 days\. For example, the following command restores the table to the `EarliestRestorableDateTime`\.  

   ```
   aws dynamodb restore-table-to-point-in-time \
       --source-table-name Music \
       --target-table-name MusicEarliestRestorableDateTime \
       --no-use-latest-restorable-time \
       --restore-date-time 1519257118.0
   ```
 Specifying the `--no-use-latest-restorable-time` argument is optional when restoring to a specific point in time\.

1. Restore the table to a point in time with custom table settings\. In this case, the `Music` table is restored to the `LatestRestorableDateTime` \(\~5 minutes ago\)\.

   You can specify a different encryption mode for the restored table, as follows\.
**Note**  
The `sse-specification-override` parameter takes the same values as the `sse-specification-override` parameter used in the `CreateTable` command\. To learn more, see [Managing encrypted tables in DynamoDB](encryption.tutorial.md)\.

   ```
   aws dynamodb restore-table-to-point-in-time \
       --source-table-name Music \
       --target-table-name MusicMinutesAgo \
       --use-latest-restorable-time \
       --sse-specification-override Enabled=true,SSEType=KMS,KMSMasterKeyId=abcd1234-abcd-1234-a123-ab1234a1b234
   ```

   You can restore the table to a different AWS Region from where the source table resides\.
**Note**  
The `sse-specification-override` parameter is mandatory for cross\-Region restores but optional for restores to the same Region as the source table\.
The `source-table-arn` parameter must be provided for cross\-Region restores\.
When performing a cross\-Region restore from the command line, you must set the default AWS Region to the desired destination Region\. To learn more, see [Command line options](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-options.html) in the *AWS Command Line Interface User Guide*\.

   ```
   aws dynamodb restore-table-to-point-in-time \
       --source-table-arn arn:aws:dynamodb:us-east-1:123456789012:table/Music \
       --target-table-name MusicMinutesAgo \
       --use-latest-restorable-time \
       --sse-specification-override Enabled=true,SSEType=KMS,KMSMasterKeyId=abcd1234-abcd-1234-a123-ab1234a1b234
   ```

   You can override the billing mode and the provisioned throughput for the restored table\.

   ```
   aws dynamodb restore-table-to-point-in-time \
       --source-table-name Music \
       --target-table-name MusicMinutesAgo \
       --use-latest-restorable-time \
       --billing-mode-override PAY_PER_REQUEST
   ```

   You can exclude some or all secondary indexes from being created on the restored table\.
**Note**  
Restores can be faster and more cost\-efficient if you exclude some or all secondary indexes from being created on the new restored table\.

   ```
   aws dynamodb restore-table-to-point-in-time \
       --source-table-name Music \
       --target-table-name MusicMinutesAgo \
       --use-latest-restorable-time \
       --global-secondary-index-override '[]'
   ```

   You can use a combination of different overrides\. For example, you can use a single global secondary index and change provisioned throughput at the same time, as follows\.

   ```
   aws dynamodb restore-table-to-point-in-time \
       --source-table-name Music \
       --target-table-name MusicMinutesAgo \
       --billing-mode-override PROVISIONED \
       --provisioned-throughput-override ReadCapacityUnits=100,WriteCapacityUnits=100 \
       --global-secondary-index-override IndexName=singers-index,KeySchema=["{AttributeName=SingerName,KeyType=HASH}"],Projection="{ProjectionType=KEYS_ONLY}",ProvisionedThroughput="{ReadCapacityUnits=50,WriteCapacityUnits=50}" \
       --sse-specification-override Enabled=true,SSEType=KMS \
       --use-latest-restorable-time
   ```

To verify the restore, use the `describe-table` command to describe the `MusicEarliestRestorableDateTime` table\.

```
aws dynamodb describe-table --table-name MusicEarliestRestorableDateTime
```

The table that is being restored is shown with the status **Creating** and restore in progress as **true**\. After the restore process is finished, the status of the `MusicEarliestRestorableDateTime` table changes to **Active**\.

**Important**  
While a restore is in progress, don't modify or delete the AWS Identity and Access Management \(IAM\) policies that grant the IAM entity \(for example, user, group, or role\) permission to perform the restore\. Otherwise, unexpected behavior can result\. For example, suppose that you remove write permissions for a table while that table is being restored\. In this case, the underlying `RestoreTableToPointInTime` operation can't write any of the restored data to the table\. IAM policies involving source IP restrictions for accessing the target restore table can similarly cause issues\.  
You can modify or delete permissions only after the restore operation is completed\.