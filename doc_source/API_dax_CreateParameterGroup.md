# CreateParameterGroup<a name="API_dax_CreateParameterGroup"></a>

Creates a new parameter group\. A parameter group is a collection of parameters that you apply to all of the nodes in a DAX cluster\.

## Request Syntax<a name="API_dax_CreateParameterGroup_RequestSyntax"></a>

```
{
   "Description": "string",
   "ParameterGroupName": "string"
}
```

## Request Parameters<a name="API_dax_CreateParameterGroup_RequestParameters"></a>

The request accepts the following data in JSON format\.

**Note**  
In the following list, the required parameters are described first\.

 ** ParameterGroupName **   
The name of the parameter group to apply to all of the clusters in this replication group\.  
Type: String  
Required: Yes

 ** Description **   
A description of the parameter group\.  
Type: String  
Required: No

## Response Syntax<a name="API_dax_CreateParameterGroup_ResponseSyntax"></a>

```
{
   "ParameterGroup": { 
      "Description": "string",
      "ParameterGroupName": "string"
   }
}
```

## Response Elements<a name="API_dax_CreateParameterGroup_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** ParameterGroup **   
Represents the output of a *CreateParameterGroup* action\.  
Type: [ParameterGroup](API_dax_ParameterGroup.md) object

## Errors<a name="API_dax_CreateParameterGroup_Errors"></a>

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

 **ParameterGroupAlreadyExistsFault**   
The specified parameter group already exists\.  
HTTP Status Code: 400

 **ParameterGroupQuotaExceededFault**   
You have attempted to exceed the maximum number of parameter groups\.  
HTTP Status Code: 400

## See Also<a name="API_dax_CreateParameterGroup_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/dax-2017-04-19/CreateParameterGroup) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/dax-2017-04-19/CreateParameterGroup) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dax-2017-04-19/CreateParameterGroup) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dax-2017-04-19/CreateParameterGroup) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dax-2017-04-19/CreateParameterGroup) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/dax-2017-04-19/CreateParameterGroup) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/dax-2017-04-19/CreateParameterGroup) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/dax-2017-04-19/CreateParameterGroup) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dax-2017-04-19/CreateParameterGroup) 