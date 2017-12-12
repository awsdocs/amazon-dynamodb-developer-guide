# DescribeTimeToLive<a name="API_DescribeTimeToLive"></a>

Gives a description of the Time to Live \(TTL\) status on the specified table\. 

## Request Syntax<a name="API_DescribeTimeToLive_RequestSyntax"></a>

```
{
   "TableName": "string"
}
```

## Request Parameters<a name="API_DescribeTimeToLive_RequestParameters"></a>

The request accepts the following data in JSON format\.

**Note**  
In the following list, the required parameters are described first\.

 ** TableName **   
The name of the table to be described\.  
Type: String  
Length Constraints: Minimum length of 3\. Maximum length of 255\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

## Response Syntax<a name="API_DescribeTimeToLive_ResponseSyntax"></a>

```
{
   "TimeToLiveDescription": { 
      "AttributeName": "string",
      "TimeToLiveStatus": "string"
   }
}
```

## Response Elements<a name="API_DescribeTimeToLive_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** TimeToLiveDescription **   
  
Type: [TimeToLiveDescription](API_TimeToLiveDescription.md) object

## Errors<a name="API_DescribeTimeToLive_Errors"></a>

For information about the errors that are common to all actions, see [Common Errors](CommonErrors.md)\.

 **InternalServerError**   
An error occurred on the server side\.  
HTTP Status Code: 500

 **ResourceNotFoundException**   
The operation tried to access a nonexistent table or index\. The resource might not be specified correctly, or its status might not be `ACTIVE`\.  
HTTP Status Code: 400

## See Also<a name="API_DescribeTimeToLive_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/dynamodb-2012-08-10/DescribeTimeToLive) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/dynamodb-2012-08-10/DescribeTimeToLive) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/DescribeTimeToLive) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/DescribeTimeToLive) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/DescribeTimeToLive) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/dynamodb-2012-08-10/DescribeTimeToLive) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/dynamodb-2012-08-10/DescribeTimeToLive) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/dynamodb-2012-08-10/DescribeTimeToLive) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/DescribeTimeToLive) 