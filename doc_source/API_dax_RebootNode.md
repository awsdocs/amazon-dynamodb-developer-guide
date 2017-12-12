# RebootNode<a name="API_dax_RebootNode"></a>

Reboots a single node of a DAX cluster\. The reboot action takes place as soon as possible\. During the reboot, the node status is set to REBOOTING\.

## Request Syntax<a name="API_dax_RebootNode_RequestSyntax"></a>

```
{
   "ClusterName": "string",
   "NodeId": "string"
}
```

## Request Parameters<a name="API_dax_RebootNode_RequestParameters"></a>

The request accepts the following data in JSON format\.

**Note**  
In the following list, the required parameters are described first\.

 ** ClusterName **   
The name of the DAX cluster containing the node to be rebooted\.  
Type: String  
Required: Yes

 ** NodeId **   
The system\-assigned ID of the node to be rebooted\.  
Type: String  
Required: Yes

## Response Syntax<a name="API_dax_RebootNode_ResponseSyntax"></a>

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

## Response Elements<a name="API_dax_RebootNode_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** Cluster **   
A description of the DAX cluster after a node has been rebooted\.  
Type: [Cluster](API_dax_Cluster.md) object

## Errors<a name="API_dax_RebootNode_Errors"></a>

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

## See Also<a name="API_dax_RebootNode_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/dax-2017-04-19/RebootNode) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/dax-2017-04-19/RebootNode) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dax-2017-04-19/RebootNode) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dax-2017-04-19/RebootNode) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dax-2017-04-19/RebootNode) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/dax-2017-04-19/RebootNode) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/dax-2017-04-19/RebootNode) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/dax-2017-04-19/RebootNode) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dax-2017-04-19/RebootNode) 