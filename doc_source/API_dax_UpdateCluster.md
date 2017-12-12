# UpdateCluster<a name="API_dax_UpdateCluster"></a>

Modifies the settings for a DAX cluster\. You can use this action to change one or more cluster configuration parameters by specifying the parameters and the new values\.

## Request Syntax<a name="API_dax_UpdateCluster_RequestSyntax"></a>

```
{
   "ClusterName": "string",
   "Description": "string",
   "NotificationTopicArn": "string",
   "NotificationTopicStatus": "string",
   "ParameterGroupName": "string",
   "PreferredMaintenanceWindow": "string",
   "SecurityGroupIds": [ "string" ]
}
```

## Request Parameters<a name="API_dax_UpdateCluster_RequestParameters"></a>

The request accepts the following data in JSON format\.

**Note**  
In the following list, the required parameters are described first\.

 ** ClusterName **   
The name of the DAX cluster to be modified\.  
Type: String  
Required: Yes

 ** Description **   
A description of the changes being made to the cluster\.  
Type: String  
Required: No

 ** NotificationTopicArn **   
The Amazon Resource Name \(ARN\) that identifies the topic\.  
Type: String  
Required: No

 ** NotificationTopicStatus **   
The current state of the topic\.  
Type: String  
Required: No

 ** ParameterGroupName **   
The name of a parameter group for this cluster\.  
Type: String  
Required: No

 ** PreferredMaintenanceWindow **   
A range of time when maintenance of DAX cluster software will be performed\. For example: `sun:01:00-sun:09:00`\. Cluster maintenance normally takes less than 30 minutes, and is performed automatically within the maintenance window\.  
Type: String  
Required: No

 ** SecurityGroupIds **   
A list of user\-specified security group IDs to be assigned to each node in the DAX cluster\. If this parameter is not specified, DAX assigns the default VPC security group to each node\.  
Type: Array of strings  
Required: No

## Response Syntax<a name="API_dax_UpdateCluster_ResponseSyntax"></a>

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

## Response Elements<a name="API_dax_UpdateCluster_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** Cluster **   
A description of the DAX cluster, after it has been modified\.  
Type: [Cluster](API_dax_Cluster.md) object

## Errors<a name="API_dax_UpdateCluster_Errors"></a>

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

 **InvalidParameterGroupStateFault**   
One or more parameters in a parameter group are in an invalid state\.  
HTTP Status Code: 400

 **InvalidParameterValueException**   
The value for a parameter is invalid\.  
HTTP Status Code: 400

 **ParameterGroupNotFoundFault**   
The specified parameter group does not exist\.  
HTTP Status Code: 400

## See Also<a name="API_dax_UpdateCluster_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/dax-2017-04-19/UpdateCluster) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/dax-2017-04-19/UpdateCluster) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dax-2017-04-19/UpdateCluster) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dax-2017-04-19/UpdateCluster) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dax-2017-04-19/UpdateCluster) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/dax-2017-04-19/UpdateCluster) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/dax-2017-04-19/UpdateCluster) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/dax-2017-04-19/UpdateCluster) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dax-2017-04-19/UpdateCluster) 