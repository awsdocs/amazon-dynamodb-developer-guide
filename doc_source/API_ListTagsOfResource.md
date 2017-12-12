# ListTagsOfResource<a name="API_ListTagsOfResource"></a>

List all tags on an Amazon DynamoDB resource\. You can call ListTagsOfResource up to 10 times per second, per account\.

For an overview on tagging DynamoDB resources, see [Tagging for DynamoDB](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Tagging.html) in the *Amazon DynamoDB Developer Guide*\.

## Request Syntax<a name="API_ListTagsOfResource_RequestSyntax"></a>

```
{
   "NextToken": "string",
   "ResourceArn": "string"
}
```

## Request Parameters<a name="API_ListTagsOfResource_RequestParameters"></a>

The request accepts the following data in JSON format\.

**Note**  
In the following list, the required parameters are described first\.

 ** ResourceArn **   
The Amazon DynamoDB resource with tags to be listed\. This value is an Amazon Resource Name \(ARN\)\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 1283\.  
Required: Yes

 ** NextToken **   
An optional string that, if supplied, must be copied from the output of a previous call to ListTagOfResource\. When provided in this manner, this API fetches the next page of results\.  
Type: String  
Required: No

## Response Syntax<a name="API_ListTagsOfResource_ResponseSyntax"></a>

```
{
   "NextToken": "string",
   "Tags": [ 
      { 
         "Key": "string",
         "Value": "string"
      }
   ]
}
```

## Response Elements<a name="API_ListTagsOfResource_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** NextToken **   
If this value is returned, there are additional results to be displayed\. To retrieve them, call ListTagsOfResource again, with NextToken set to this value\.  
Type: String

 ** Tags **   
The tags currently associated with the Amazon DynamoDB resource\.  
Type: Array of [Tag](API_Tag.md) objects

## Errors<a name="API_ListTagsOfResource_Errors"></a>

For information about the errors that are common to all actions, see [Common Errors](CommonErrors.md)\.

 **InternalServerError**   
An error occurred on the server side\.  
HTTP Status Code: 500

 **ResourceNotFoundException**   
The operation tried to access a nonexistent table or index\. The resource might not be specified correctly, or its status might not be `ACTIVE`\.  
HTTP Status Code: 400

## See Also<a name="API_ListTagsOfResource_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/dynamodb-2012-08-10/ListTagsOfResource) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/dynamodb-2012-08-10/ListTagsOfResource) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/ListTagsOfResource) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/ListTagsOfResource) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/ListTagsOfResource) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/dynamodb-2012-08-10/ListTagsOfResource) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/dynamodb-2012-08-10/ListTagsOfResource) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/dynamodb-2012-08-10/ListTagsOfResource) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/ListTagsOfResource) 