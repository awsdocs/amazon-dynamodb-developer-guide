# DescribeClusters<a name="API_dax_DescribeClusters"></a>

Returns information about all provisioned DAX clusters if no cluster identifier is specified, or about a specific DAX cluster if a cluster identifier is supplied\.

If the cluster is in the CREATING state, only cluster level information will be displayed until all of the nodes are successfully provisioned\.

If the cluster is in the DELETING state, only cluster level information will be displayed\.

If nodes are currently being added to the DAX cluster, node endpoint information and creation time for the additional nodes will not be displayed until they are completely provisioned\. When the DAX cluster state is *available*, the cluster is ready for use\.

If nodes are currently being removed from the DAX cluster, no endpoint information for the removed nodes is displayed\.

## Request Syntax<a name="API_dax_DescribeClusters_RequestSyntax"></a>

```
{
   "ClusterNames": [ "string" ],
   "MaxResults": number,
   "NextToken": "string"
}
```

## Request Parameters<a name="API_dax_DescribeClusters_RequestParameters"></a>

The request accepts the following data in JSON format\.

**Note**  
In the following list, the required parameters are described first\.

 ** ClusterNames **   
The names of the DAX clusters being described\.  
Type: Array of strings  
Required: No

 ** MaxResults **   
The maximum number of results to include in the response\. If more results exist than the specified `MaxResults` value, a token is included in the response so that the remaining results can be retrieved\.  
The value for `MaxResults` must be between 20 and 100\.  
Type: Integer  
Required: No

 ** NextToken **   
An optional token returned from a prior request\. Use this token for pagination of results from this action\. If this parameter is specified, the response includes only results beyond the token, up to the value specified by `MaxResults`\.  
Type: String  
Required: No

## Response Syntax<a name="API_dax_DescribeClusters_ResponseSyntax"></a>

```
{
   "Clusters": [ 
      { 
         "ActiveNodes": number,
         "ClusterArn": "string",
         "ClusterDiscoveryEndpoint": { 
            "Address": "string",
            "Port": number
         },
         "ClusterName": "string",
         "Description": "string",
         "IamRoleArn": "string",
         "NodeIdsToRemove": [ "string" ],
         "Nodes": [ 
            { 
               "AvailabilityZone": "string",
               "Endpoint": { 
                  "Address": "string",
                  "Port": number
               },
               "NodeCreateTime": number,
               "NodeId": "string",
               "NodeStatus": "string",
               "ParameterGroupStatus": "string"
            }
         ],
         "NodeType": "string",
         "NotificationConfiguration": { 
            "TopicArn": "string",
            "TopicStatus": "string"
         },
         "ParameterGroup": { 
            "NodeIdsToReboot": [ "string" ],
            "ParameterApplyStatus": "string",
            "ParameterGroupName": "string"
         },
         "PreferredMaintenanceWindow": "string",
         "SecurityGroups": [ 
            { 
               "SecurityGroupIdentifier": "string",
               "Status": "string"
            }
         ],
         "Status": "string",
         "SubnetGroup": "string",
         "TotalNodes": number
      }
   ],
   "NextToken": "string"
}
```

## Response Elements<a name="API_dax_DescribeClusters_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** Clusters **   
The descriptions of your DAX clusters, in response to a *DescribeClusters* request\.  
Type: Array of [Cluster](API_dax_Cluster.md) objects

 ** NextToken **   
Provides an identifier to allow retrieval of paginated results\.  
Type: String

## Errors<a name="API_dax_DescribeClusters_Errors"></a>

For information about the errors that are common to all actions, see [Common Errors](CommonErrors.md)\.

 **ClusterNotFoundFault**   
The requested cluster ID does not refer to an existing DAX cluster\.  
HTTP Status Code: 400

 **InvalidParameterCombinationException**   
Two or more incompatible parameters were specified\.  
HTTP Status Code: 400

 **InvalidParameterValueException**   
The value for a parameter is invalid\.  
HTTP Status Code: 400

## See Also<a name="API_dax_DescribeClusters_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/dax-2017-04-19/DescribeClusters) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/dax-2017-04-19/DescribeClusters) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dax-2017-04-19/DescribeClusters) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dax-2017-04-19/DescribeClusters) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dax-2017-04-19/DescribeClusters) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/dax-2017-04-19/DescribeClusters) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/dax-2017-04-19/DescribeClusters) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/dax-2017-04-19/DescribeClusters) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dax-2017-04-19/DescribeClusters) 