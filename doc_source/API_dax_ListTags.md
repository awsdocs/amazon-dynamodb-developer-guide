# ListTags<a name="API_dax_ListTags"></a>

List all of the tags for a DAX cluster\. You can call `ListTags` up to 10 times per second, per account\.

## Request Syntax<a name="API_dax_ListTags_RequestSyntax"></a>

```
{
   "NextToken": "string",
   "ResourceName": "string"
}
```

## Request Parameters<a name="API_dax_ListTags_RequestParameters"></a>

The request accepts the following data in JSON format\.

**Note**  
In the following list, the required parameters are described first\.

 ** ResourceName **   
The name of the DAX resource to which the tags belong\.  
Type: String  
Required: Yes

 ** NextToken **   
An optional token returned from a prior request\. Use this token for pagination of results from this action\. If this parameter is specified, the response includes only results beyond the token\.  
Type: String  
Required: No

## Response Syntax<a name="API_dax_ListTags_ResponseSyntax"></a>

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

## Response Elements<a name="API_dax_ListTags_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** NextToken **   
If this value is present, there are additional results to be displayed\. To retrieve them, call `ListTags` again, with `NextToken` set to this value\.  
Type: String

 ** Tags **   
A list of tags currently associated with the DAX cluster\.  
Type: Array of [Tag](API_dax_Tag.md) objects

## Errors<a name="API_dax_ListTags_Errors"></a>

For information about the errors that are common to all actions, see [Common Errors](CommonErrors.md)\.

 **ClusterNotFoundFault**   
The requested cluster ID does not refer to an existing DAX cluster\.  
HTTP Status Code: 400

 **InvalidARNFault**   
The Amazon Resource Name \(ARN\) supplied in the request is not valid\.  
HTTP Status Code: 400

 **InvalidClusterStateFault**   
The requested DAX cluster is not in the *available* state\.  
HTTP Status Code: 400

 **InvalidParameterCombinationException**   
Two or more incompatible parameters were specified\.  
HTTP Status Code: 400

 **InvalidParameterValueException**   
The value for a parameter is invalid\.  
HTTP Status Code: 400

## See Also<a name="API_dax_ListTags_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/dax-2017-04-19/ListTags) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/dax-2017-04-19/ListTags) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dax-2017-04-19/ListTags) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dax-2017-04-19/ListTags) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dax-2017-04-19/ListTags) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/dax-2017-04-19/ListTags) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/dax-2017-04-19/ListTags) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/dax-2017-04-19/ListTags) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dax-2017-04-19/ListTags) 