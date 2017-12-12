# DescribeContinuousBackups<a name="API_DescribeContinuousBackups"></a>

Checks the status of the backup restore settings on the specified table\. If backups are enabled, `ContinuousBackupsStatus` will bet set to ENABLED\.

You can call `DescribeContinuousBackups` at a maximum rate of 10 times per second\.

## Request Syntax<a name="API_DescribeContinuousBackups_RequestSyntax"></a>

```
{
   "TableName": "string"
}
```

## Request Parameters<a name="API_DescribeContinuousBackups_RequestParameters"></a>

The request accepts the following data in JSON format\.

**Note**  
In the following list, the required parameters are described first\.

 ** TableName **   
Name of the table for which the customer wants to check the backup and restore settings\.  
Type: String  
Length Constraints: Minimum length of 3\. Maximum length of 255\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

## Response Syntax<a name="API_DescribeContinuousBackups_ResponseSyntax"></a>

```
{
   "ContinuousBackupsDescription": { 
      "ContinuousBackupsStatus": "string"
   }
}
```

## Response Elements<a name="API_DescribeContinuousBackups_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** ContinuousBackupsDescription **   
 `ContinuousBackupsDescription` can be one of the following : ENABLED, DISABLED\.   
Type: [ContinuousBackupsDescription](API_ContinuousBackupsDescription.md) object

## Errors<a name="API_DescribeContinuousBackups_Errors"></a>

For information about the errors that are common to all actions, see [Common Errors](CommonErrors.md)\.

 **InternalServerError**   
An error occurred on the server side\.  
HTTP Status Code: 500

 **TableNotFoundException**   
A table with the name `TableName` does not currently exist within the subscriber's account\.  
HTTP Status Code: 400

## See Also<a name="API_DescribeContinuousBackups_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/dynamodb-2012-08-10/DescribeContinuousBackups) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/dynamodb-2012-08-10/DescribeContinuousBackups) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/DescribeContinuousBackups) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/DescribeContinuousBackups) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/DescribeContinuousBackups) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/dynamodb-2012-08-10/DescribeContinuousBackups) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/dynamodb-2012-08-10/DescribeContinuousBackups) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/dynamodb-2012-08-10/DescribeContinuousBackups) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/DescribeContinuousBackups) 