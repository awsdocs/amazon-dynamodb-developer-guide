# DeleteParameterGroup<a name="API_dax_DeleteParameterGroup"></a>

Deletes the specified parameter group\. You cannot delete a parameter group if it is associated with any DAX clusters\.

## Request Syntax<a name="API_dax_DeleteParameterGroup_RequestSyntax"></a>

```
{
   "ParameterGroupName": "string"
}
```

## Request Parameters<a name="API_dax_DeleteParameterGroup_RequestParameters"></a>

The request accepts the following data in JSON format\.

**Note**  
In the following list, the required parameters are described first\.

 ** ParameterGroupName **   
The name of the parameter group to delete\.  
Type: String  
Required: Yes

## Response Syntax<a name="API_dax_DeleteParameterGroup_ResponseSyntax"></a>

```
{
   "DeletionMessage": "string"
}
```

## Response Elements<a name="API_dax_DeleteParameterGroup_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** DeletionMessage **   
A user\-specified message for this action \(i\.e\., a reason for deleting the parameter group\)\.  
Type: String

## Errors<a name="API_dax_DeleteParameterGroup_Errors"></a>

For information about the errors that are common to all actions, see [Common Errors](CommonErrors.md)\.

 **InvalidParameterCombinationException**   
Two or more incompatible parameters were specified\.  
HTTP Status Code: 400

 **InvalidParameterGroupStateFault**   
One or more parameters in a parameter group are in an invalid state\.  
HTTP Status Code: 400

 **InvalidParameterValueException**   
The value for a parameter is invalid\.  
HTTP Status Code: 400

 **ParameterGroupNotFoundFault**   
The specified parameter group does not exist\.  
HTTP Status Code: 400

## See Also<a name="API_dax_DeleteParameterGroup_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/dax-2017-04-19/DeleteParameterGroup) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/dax-2017-04-19/DeleteParameterGroup) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dax-2017-04-19/DeleteParameterGroup) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dax-2017-04-19/DeleteParameterGroup) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dax-2017-04-19/DeleteParameterGroup) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/dax-2017-04-19/DeleteParameterGroup) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/dax-2017-04-19/DeleteParameterGroup) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/dax-2017-04-19/DeleteParameterGroup) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dax-2017-04-19/DeleteParameterGroup) 