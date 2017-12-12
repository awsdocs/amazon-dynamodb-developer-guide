# DescribeSubnetGroups<a name="API_dax_DescribeSubnetGroups"></a>

Returns a list of subnet group descriptions\. If a subnet group name is specified, the list will contain only the description of that group\.

## Request Syntax<a name="API_dax_DescribeSubnetGroups_RequestSyntax"></a>

```
{
   "MaxResults": number,
   "NextToken": "string",
   "SubnetGroupNames": [ "string" ]
}
```

## Request Parameters<a name="API_dax_DescribeSubnetGroups_RequestParameters"></a>

The request accepts the following data in JSON format\.

**Note**  
In the following list, the required parameters are described first\.

 ** MaxResults **   
The maximum number of results to include in the response\. If more results exist than the specified `MaxResults` value, a token is included in the response so that the remaining results can be retrieved\.  
The value for `MaxResults` must be between 20 and 100\.  
Type: Integer  
Required: No

 ** NextToken **   
An optional token returned from a prior request\. Use this token for pagination of results from this action\. If this parameter is specified, the response includes only results beyond the token, up to the value specified by `MaxResults`\.  
Type: String  
Required: No

 ** SubnetGroupNames **   
The name of the subnet group\.  
Type: Array of strings  
Required: No

## Response Syntax<a name="API_dax_DescribeSubnetGroups_ResponseSyntax"></a>

```
{
   "NextToken": "string",
   "SubnetGroups": [ 
      { 
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
   ]
}
```

## Response Elements<a name="API_dax_DescribeSubnetGroups_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** NextToken **   
Provides an identifier to allow retrieval of paginated results\.  
Type: String

 ** SubnetGroups **   
An array of subnet groups\. Each element in the array represents a single subnet group\.  
Type: Array of [SubnetGroup](API_dax_SubnetGroup.md) objects

## Errors<a name="API_dax_DescribeSubnetGroups_Errors"></a>

For information about the errors that are common to all actions, see [Common Errors](CommonErrors.md)\.

 **SubnetGroupNotFoundFault**   
The requested subnet group name does not refer to an existing subnet group\.  
HTTP Status Code: 400

## See Also<a name="API_dax_DescribeSubnetGroups_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/dax-2017-04-19/DescribeSubnetGroups) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/dax-2017-04-19/DescribeSubnetGroups) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dax-2017-04-19/DescribeSubnetGroups) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dax-2017-04-19/DescribeSubnetGroups) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dax-2017-04-19/DescribeSubnetGroups) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/dax-2017-04-19/DescribeSubnetGroups) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/dax-2017-04-19/DescribeSubnetGroups) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/dax-2017-04-19/DescribeSubnetGroups) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dax-2017-04-19/DescribeSubnetGroups) 