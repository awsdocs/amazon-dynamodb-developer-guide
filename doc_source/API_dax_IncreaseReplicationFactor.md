# IncreaseReplicationFactor<a name="API_dax_IncreaseReplicationFactor"></a>

Adds one or more nodes to a DAX cluster\.

## Request Syntax<a name="API_dax_IncreaseReplicationFactor_RequestSyntax"></a>

```
{
   "AvailabilityZones": [ "string" ],
   "ClusterName": "string",
   "NewReplicationFactor": number
}
```

## Request Parameters<a name="API_dax_IncreaseReplicationFactor_RequestParameters"></a>

The request accepts the following data in JSON format\.

**Note**  
In the following list, the required parameters are described first\.

 ** ClusterName **   
The name of the DAX cluster that will receive additional nodes\.  
Type: String  
Required: Yes

 ** NewReplicationFactor **   
The new number of nodes for the DAX cluster\.  
Type: Integer  
Required: Yes

 ** AvailabilityZones **   
The Availability Zones \(AZs\) in which the cluster nodes will be created\. All nodes belonging to the cluster are placed in these Availability Zones\. Use this parameter if you want to distribute the nodes across multiple AZs\.  
Type: Array of strings  
Required: No

## Response Syntax<a name="API_dax_IncreaseReplicationFactor_ResponseSyntax"></a>

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

## Response Elements<a name="API_dax_IncreaseReplicationFactor_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** Cluster **   
A description of the DAX cluster\. with its new replication factor\.  
Type: [Cluster](API_dax_Cluster.md) object

## Errors<a name="API_dax_IncreaseReplicationFactor_Errors"></a>

For information about the errors that are common to all actions, see [Common Errors](CommonErrors.md)\.

 **ClusterNotFoundFault**   
The requested cluster ID does not refer to an existing DAX cluster\.  
HTTP Status Code: 400

 **InsufficientClusterCapacityFault**   
There are not enough system resources to create the cluster you requested \(or to resize an already\-existing cluster\)\.   
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

 **InvalidVPCNetworkStateFault**   
The VPC network is in an invalid state\.  
HTTP Status Code: 400

 **NodeQuotaForClusterExceededFault**   
You have attempted to exceed the maximum number of nodes for a DAX cluster\.  
HTTP Status Code: 400

 **NodeQuotaForCustomerExceededFault**   
You have attempted to exceed the maximum number of nodes for your AWS account\.  
HTTP Status Code: 400

## See Also<a name="API_dax_IncreaseReplicationFactor_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/dax-2017-04-19/IncreaseReplicationFactor) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/dax-2017-04-19/IncreaseReplicationFactor) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dax-2017-04-19/IncreaseReplicationFactor) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dax-2017-04-19/IncreaseReplicationFactor) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dax-2017-04-19/IncreaseReplicationFactor) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/dax-2017-04-19/IncreaseReplicationFactor) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/dax-2017-04-19/IncreaseReplicationFactor) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/dax-2017-04-19/IncreaseReplicationFactor) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dax-2017-04-19/IncreaseReplicationFactor) 