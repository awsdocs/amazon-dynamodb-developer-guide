# ListBackups<a name="API_ListBackups"></a>

List backups associated with an AWS account\. To list backups for a given table, specify `TableName`\. `ListBackups` returns a paginated list of results with at most 1MB worth of items in a page\. You can also specify a limit for the maximum number of entries to be returned in a page\. 

In the request, start time is inclusive but end time is exclusive\. Note that these limits are for the time at which the original backup was requested\.

You can call `ListBackups` a maximum of 5 times per second\.

## Request Syntax<a name="API_ListBackups_RequestSyntax"></a>

```
{
   "ExclusiveStartBackupArn": "string",
   "Limit": number,
   "TableName": "string",
   "TimeRangeLowerBound": number,
   "TimeRangeUpperBound": number
}
```

## Request Parameters<a name="API_ListBackups_RequestParameters"></a>

The request accepts the following data in JSON format\.

**Note**  
In the following list, the required parameters are described first\.

 ** ExclusiveStartBackupArn **   
 `LastEvaluatedBackupARN` returned by the previous ListBackups call\.   
Type: String  
Length Constraints: Minimum length of 37\. Maximum length of 1024\.  
Required: No

 ** Limit **   
Maximum number of backups to return at once\.  
Type: Integer  
Valid Range: Minimum value of 1\. Maximum value of 100\.  
Required: No

 ** TableName **   
The backups from the table specified by TableName are listed\.   
Type: String  
Length Constraints: Minimum length of 3\. Maximum length of 255\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: No

 ** TimeRangeLowerBound **   
Only backups created after this time are listed\. `TimeRangeLowerBound` is inclusive\.  
Type: Timestamp  
Required: No

 ** TimeRangeUpperBound **   
Only backups created before this time are listed\. `TimeRangeUpperBound` is exclusive\.   
Type: Timestamp  
Required: No

## Response Syntax<a name="API_ListBackups_ResponseSyntax"></a>

```
{
   "BackupSummaries": [ 
      { 
         "BackupArn": "string",
         "BackupCreationDateTime": number,
         "BackupName": "string",
         "BackupSizeBytes": number,
         "BackupStatus": "string",
         "TableArn": "string",
         "TableId": "string",
         "TableName": "string"
      }
   ],
   "LastEvaluatedBackupArn": "string"
}
```

## Response Elements<a name="API_ListBackups_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** BackupSummaries **   
List of `BackupSummary` objects\.  
Type: Array of [BackupSummary](API_BackupSummary.md) objects

 ** LastEvaluatedBackupArn **   
Last evaluated BackupARN\.  
Type: String  
Length Constraints: Minimum length of 37\. Maximum length of 1024\.

## Errors<a name="API_ListBackups_Errors"></a>

For information about the errors that are common to all actions, see [Common Errors](CommonErrors.md)\.

 **InternalServerError**   
An error occurred on the server side\.  
HTTP Status Code: 500

## See Also<a name="API_ListBackups_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/dynamodb-2012-08-10/ListBackups) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/dynamodb-2012-08-10/ListBackups) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/ListBackups) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/ListBackups) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/ListBackups) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/dynamodb-2012-08-10/ListBackups) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/dynamodb-2012-08-10/ListBackups) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/dynamodb-2012-08-10/ListBackups) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/ListBackups) 