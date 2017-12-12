# CreateSubnetGroup<a name="API_dax_CreateSubnetGroup"></a>

Creates a new subnet group\.

## Request Syntax<a name="API_dax_CreateSubnetGroup_RequestSyntax"></a>

```
{
   "Description": "string",
   "SubnetGroupName": "string",
   "SubnetIds": [ "string" ]
}
```

## Request Parameters<a name="API_dax_CreateSubnetGroup_RequestParameters"></a>

The request accepts the following data in JSON format\.

**Note**  
In the following list, the required parameters are described first\.

 ** SubnetGroupName **   
A name for the subnet group\. This value is stored as a lowercase string\.   
Type: String  
Required: Yes

 ** SubnetIds **   
A list of VPC subnet IDs for the subnet group\.  
Type: Array of strings  
Required: Yes

 ** Description **   
A description for the subnet group  
Type: String  
Required: No

## Response Syntax<a name="API_dax_CreateSubnetGroup_ResponseSyntax"></a>

```
{
   "SubnetGroup": { 
      "Description": "string",
      "SubnetGroupName": "string",
      "Subnets": [ 
         { 
            "SubnetAvailabilityZone": "string",
            "SubnetIdentifier": "string"
         }
      ],
      "VpcId": "string"
   }
}
```

## Response Elements<a name="API_dax_CreateSubnetGroup_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** SubnetGroup **   
Represents the output of a *CreateSubnetGroup* operation\.  
Type: [SubnetGroup](API_dax_SubnetGroup.md) object

## Errors<a name="API_dax_CreateSubnetGroup_Errors"></a>

For information about the errors that are common to all actions, see [Common Errors](CommonErrors.md)\.

 **InvalidSubnet**   
An invalid subnet identifier was specified\.  
HTTP Status Code: 400

 **SubnetGroupAlreadyExistsFault**   
The specified subnet group already exists\.  
HTTP Status Code: 400

 **SubnetGroupQuotaExceededFault**   
The request cannot be processed because it would exceed the allowed number of subnets in a subnet group\.  
HTTP Status Code: 400

 **SubnetQuotaExceededFault**   
The request cannot be processed because it would exceed the allowed number of subnets in a subnet group\.  
HTTP Status Code: 400

## See Also<a name="API_dax_CreateSubnetGroup_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/dax-2017-04-19/CreateSubnetGroup) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/dax-2017-04-19/CreateSubnetGroup) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dax-2017-04-19/CreateSubnetGroup) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dax-2017-04-19/CreateSubnetGroup) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dax-2017-04-19/CreateSubnetGroup) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/dax-2017-04-19/CreateSubnetGroup) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/dax-2017-04-19/CreateSubnetGroup) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/dax-2017-04-19/CreateSubnetGroup) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dax-2017-04-19/CreateSubnetGroup) 