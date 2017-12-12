# CreateBackup<a name="API_CreateBackup"></a>

Creates a backup for an existing table\.

 Each time you create an On\-Demand Backup, the entire table data is backed up\. There is no limit to the number of on\-demand backups that can be taken\. 

 When you create an On\-Demand Backup, a time marker of the request is cataloged, and the backup is created asynchronously, by applying all changes until the time of the request to the last full table snapshot\. Backup requests are processed instantaneously and become available for restore within minutes\. 

You can call `CreateBackup` at a maximum rate of 50 times per second\.

All backups in DynamoDB work without consuming any provisioned throughput on the table\.

 If you submit a backup request on 2018\-12\-14 at 14:25:00, the backup is guaranteed to contain all data committed to the table up to 14:24:00, and data committed after 14:26:00 will not be\. The backup may or may not contain data modifications made between 14:24:00 and 14:26:00\. On\-Demand Backup does not support causal consistency\. 

 Along with data, the following are also included on the backups: 

+ Global secondary indexes \(GSIs\)

+ Local secondary indexes \(LSIs\)

+ Streams

+ Provisioned read and write capacity

## Request Syntax<a name="API_CreateBackup_RequestSyntax"></a>

```
{
   "BackupName": "string",
   "TableName": "string"
}
```

## Request Parameters<a name="API_CreateBackup_RequestParameters"></a>

The request accepts the following data in JSON format\.

**Note**  
In the following list, the required parameters are described first\.

 ** BackupName **   
Specified name for the backup\.  
Type: String  
Length Constraints: Minimum length of 3\. Maximum length of 255\.  
Required: Yes

 ** TableName **   
The name of the table\.  
Type: String  
Length Constraints: Minimum length of 3\. Maximum length of 255\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

## Response Syntax<a name="API_CreateBackup_ResponseSyntax"></a>

```
{
   "BackupDetails": { 
      "BackupArn": "string",
      "BackupCreationDateTime": number,
      "BackupName": "string",
      "BackupSizeBytes": number,
      "BackupStatus": "string"
   }
}
```

## Response Elements<a name="API_CreateBackup_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** BackupDetails **   
Contains the details of the backup created for the table\.  
Type: [BackupDetails](API_BackupDetails.md) object

## Errors<a name="API_CreateBackup_Errors"></a>

For information about the errors that are common to all actions, see [Common Errors](CommonErrors.md)\.

 **BackupInUseException**   
There is another ongoing conflicting backup control plane operation on the table\. The backups is either being created, deleted or restored to a table\.  
HTTP Status Code: 400

 **ContinuousBackupsUnavailableException**   
Backups have not yet been enabled for this table\.  
HTTP Status Code: 400

 **InternalServerError**   
An error occurred on the server side\.  
HTTP Status Code: 500

 **LimitExceededException**   
Up to 50 `CreateBackup` operations are allowed per second, per account\. There is no limit to the number of daily on\-demand backups that can be taken\.   
Up to 10 simultaneous table operations are allowed per account\. These operations include `CreateTable`, `UpdateTable`, `DeleteTable`,`UpdateTimeToLive`, and `RestoreTableFromBackup`\.   
For tables with secondary indexes, only one of those tables can be in the `CREATING` state at any point in time\. Do not attempt to create more than one such table simultaneously\.  
The total limit of tables in the `ACTIVE` state is 250\.  
For tables with secondary indexes, only one of those tables can be in the `CREATING` state at any point in time\. Do not attempt to create more than one such table simultaneously\.  
The total limit of tables in the `ACTIVE` state is 250\.  
HTTP Status Code: 400

 **TableInUseException**   
A table by that name is either being created or deleted\.   
HTTP Status Code: 400

 **TableNotFoundException**   
A table with the name `TableName` does not currently exist within the subscriber's account\.  
HTTP Status Code: 400

## See Also<a name="API_CreateBackup_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/dynamodb-2012-08-10/CreateBackup) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/dynamodb-2012-08-10/CreateBackup) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/CreateBackup) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/CreateBackup) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/CreateBackup) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/dynamodb-2012-08-10/CreateBackup) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/dynamodb-2012-08-10/CreateBackup) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/dynamodb-2012-08-10/CreateBackup) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/CreateBackup) 