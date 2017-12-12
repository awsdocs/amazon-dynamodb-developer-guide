# DecreaseReplicationFactor<a name="API_dax_DecreaseReplicationFactor"></a>

Removes one or more nodes from a DAX cluster\.

**Note**  
You cannot use `DecreaseReplicationFactor` to remove the last node in a DAX cluster\. If you need to do this, use `DeleteCluster` instead\.

## Request Syntax<a name="API_dax_DecreaseReplicationFactor_RequestSyntax"></a>

```
{
   "AvailabilityZones": [ "string" ],
   "ClusterName": "string",
   "NewReplicationFactor": number,
   "NodeIdsToRemove": [ "string" ]
}
```

## Request Parameters<a name="API_dax_DecreaseReplicationFactor_RequestParameters"></a>

The request accepts the following data in JSON format\.

**Note**  
In the following list, the required parameters are described first\.

 ** ClusterName **   
The name of the DAX cluster from which you want to remove nodes\.  
Type: String  
Required: Yes

 ** NewReplicationFactor **   
The new number of nodes for the DAX cluster\.  
Type: Integer  
Required: Yes

 ** AvailabilityZones **   
The Availability Zone\(s\) from which to remove nodes\.  
Type: Array of strings  
Required: No

 ** NodeIdsToRemove **   
The unique identifiers of the nodes to be removed from the cluster\.  
Type: Array of strings  
Required: No

## Response Syntax<a name="API_dax_DecreaseReplicationFactor_ResponseSyntax"></a>

```
{
   "Cluster": { 
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
}
```

## Response Elements<a name="API_dax_DecreaseReplicationFactor_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** Cluster **   
A description of the DAX cluster, after you have decreased its replication factor\.  
Type: [Cluster](API_dax_Cluster.md) object

## Errors<a name="API_dax_DecreaseReplicationFactor_Errors"></a>

For information about the errors that are common to all actions, see [Common Errors](CommonErrors.md)\.

 **ClusterNotFoundFault**   
The requested cluster ID does not refer to an existing DAX cluster\.  
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

 **NodeNotFoundFault**   
None of the nodes in the cluster have the given node ID\.  
HTTP Status Code: 400

## See Also<a name="API_dax_DecreaseReplicationFactor_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/dax-2017-04-19/DecreaseReplicationFactor) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/dax-2017-04-19/DecreaseReplicationFactor) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dax-2017-04-19/DecreaseReplicationFactor) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dax-2017-04-19/DecreaseReplicationFactor) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dax-2017-04-19/DecreaseReplicationFactor) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/dax-2017-04-19/DecreaseReplicationFactor) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/dax-2017-04-19/DecreaseReplicationFactor) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/dax-2017-04-19/DecreaseReplicationFactor) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dax-2017-04-19/DecreaseReplicationFactor) 