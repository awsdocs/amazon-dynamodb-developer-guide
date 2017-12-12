# UpdateTimeToLive<a name="API_UpdateTimeToLive"></a>

The UpdateTimeToLive method will enable or disable TTL for the specified table\. A successful `UpdateTimeToLive` call returns the current `TimeToLiveSpecification`; it may take up to one hour for the change to fully process\. Any additional `UpdateTimeToLive` calls for the same table during this one hour duration result in a `ValidationException`\. 

TTL compares the current time in epoch time format to the time stored in the TTL attribute of an item\. If the epoch time value stored in the attribute is less than the current time, the item is marked as expired and subsequently deleted\.

**Note**  
 The epoch time format is the number of seconds elapsed since 12:00:00 AM January 1st, 1970 UTC\. 

DynamoDB deletes expired items on a best\-effort basis to ensure availability of throughput for other data operations\. 

**Important**  
DynamoDB typically deletes expired items within two days of expiration\. The exact duration within which an item gets deleted after expiration is specific to the nature of the workload\. Items that have expired and not been deleted will still show up in reads, queries, and scans\.

As items are deleted, they are removed from any Local Secondary Index and Global Secondary Index immediately in the same eventually consistent way as a standard delete operation\.

For more information, see [Time To Live](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/TTL.html) in the Amazon DynamoDB Developer Guide\. 

## Request Syntax<a name="API_UpdateTimeToLive_RequestSyntax"></a>

```
{
   "TableName": "string",
   "TimeToLiveSpecification": { 
      "AttributeName": "string",
      "Enabled": boolean
   }
}
```

## Request Parameters<a name="API_UpdateTimeToLive_RequestParameters"></a>

The request accepts the following data in JSON format\.

**Note**  
In the following list, the required parameters are described first\.

 ** TableName **   
The name of the table to be configured\.  
Type: String  
Length Constraints: Minimum length of 3\. Maximum length of 255\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

 ** TimeToLiveSpecification **   
Represents the settings used to enable or disable Time to Live for the specified table\.  
Type: [TimeToLiveSpecification](API_TimeToLiveSpecification.md) object  
Required: Yes

## Response Syntax<a name="API_UpdateTimeToLive_ResponseSyntax"></a>

```
{
   "TimeToLiveSpecification": { 
      "AttributeName": "string",
      "Enabled": boolean
   }
}
```

## Response Elements<a name="API_UpdateTimeToLive_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** TimeToLiveSpecification **   
Represents the output of an `UpdateTimeToLive` operation\.  
Type: [TimeToLiveSpecification](API_TimeToLiveSpecification.md) object

## Errors<a name="API_UpdateTimeToLive_Errors"></a>

For information about the errors that are common to all actions, see [Common Errors](CommonErrors.md)\.

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

 **ResourceInUseException**   
The operation conflicts with the resource's availability\. For example, you attempted to recreate an existing table, or tried to delete a table currently in the `CREATING` state\.  
HTTP Status Code: 400

 **ResourceNotFoundException**   
The operation tried to access a nonexistent table or index\. The resource might not be specified correctly, or its status might not be `ACTIVE`\.  
HTTP Status Code: 400

## See Also<a name="API_UpdateTimeToLive_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/dynamodb-2012-08-10/UpdateTimeToLive) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/dynamodb-2012-08-10/UpdateTimeToLive) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/UpdateTimeToLive) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/UpdateTimeToLive) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/UpdateTimeToLive) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/dynamodb-2012-08-10/UpdateTimeToLive) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/dynamodb-2012-08-10/UpdateTimeToLive) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/dynamodb-2012-08-10/UpdateTimeToLive) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/UpdateTimeToLive) 