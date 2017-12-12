# CreateCluster<a name="API_dax_CreateCluster"></a>

Creates a DAX cluster\. All nodes in the cluster run the same DAX caching software\.

## Request Syntax<a name="API_dax_CreateCluster_RequestSyntax"></a>

```
{
   "AvailabilityZones": [ "string" ],
   "ClusterName": "string",
   "Description": "string",
   "IamRoleArn": "string",
   "NodeType": "string",
   "NotificationTopicArn": "string",
   "ParameterGroupName": "string",
   "PreferredMaintenanceWindow": "string",
   "ReplicationFactor": number,
   "SecurityGroupIds": [ "string" ],
   "SubnetGroupName": "string",
   "Tags": [ 
      { 
         "Key": "string",
         "Value": "string"
      }
   ]
}
```

## Request Parameters<a name="API_dax_CreateCluster_RequestParameters"></a>

The request accepts the following data in JSON format\.

**Note**  
In the following list, the required parameters are described first\.

 ** ClusterName **   
The cluster identifier\. This parameter is stored as a lowercase string\.  
 **Constraints:**   

+ A name must contain from 1 to 20 alphanumeric characters or hyphens\.

+ The first character must be a letter\.

+ A name cannot end with a hyphen or contain two consecutive hyphens\.
Type: String  
Required: Yes

 ** IamRoleArn **   
A valid Amazon Resource Name \(ARN\) that identifies an IAM role\. At runtime, DAX will assume this role and use the role's permissions to access DynamoDB on your behalf\.  
Type: String  
Required: Yes

 ** NodeType **   
The compute and memory capacity of the nodes in the cluster\.  
Type: String  
Required: Yes

 ** ReplicationFactor **   
The number of nodes in the DAX cluster\. A replication factor of 1 will create a single\-node cluster, without any read replicas\. For additional fault tolerance, you can create a multiple node cluster with one or more read replicas\. To do this, set *ReplicationFactor* to 2 or more\.  
AWS recommends that you have at least two read replicas per cluster\.
Type: Integer  
Required: Yes

 ** AvailabilityZones **   
The Availability Zones \(AZs\) in which the cluster nodes will be created\. All nodes belonging to the cluster are placed in these Availability Zones\. Use this parameter if you want to distribute the nodes across multiple AZs\.  
Type: Array of strings  
Required: No

 ** Description **   
A description of the cluster\.  
Type: String  
Required: No

 ** NotificationTopicArn **   
The Amazon Resource Name \(ARN\) of the Amazon SNS topic to which notifications will be sent\.  
The Amazon SNS topic owner must be same as the DAX cluster owner\.
Type: String  
Required: No

 ** ParameterGroupName **   
The parameter group to be associated with the DAX cluster\.  
Type: String  
Required: No

 ** PreferredMaintenanceWindow **   
Specifies the weekly time range during which maintenance on the DAX cluster is performed\. It is specified as a range in the format ddd:hh24:mi\-ddd:hh24:mi \(24H Clock UTC\)\. The minimum maintenance window is a 60 minute period\. Valid values for `ddd` are:  

+  `sun` 

+  `mon` 

+  `tue` 

+  `wed` 

+  `thu` 

+  `fri` 

+  `sat` 
Example: `sun:05:00-sun:09:00`   
If you don't specify a preferred maintenance window when you create or modify a cache cluster, DAX assigns a 60\-minute maintenance window on a randomly selected day of the week\.
Type: String  
Required: No

 ** SecurityGroupIds **   
A list of security group IDs to be assigned to each node in the DAX cluster\. \(Each of the security group ID is system\-generated\.\)  
If this parameter is not specified, DAX assigns the default VPC security group to each node\.  
Type: Array of strings  
Required: No

 ** SubnetGroupName **   
The name of the subnet group to be used for the replication group\.  
DAX clusters can only run in an Amazon VPC environment\. All of the subnets that you specify in a subnet group must exist in the same VPC\.
Type: String  
Required: No

 ** Tags **   
A set of tags to associate with the DAX cluster\.   
Type: Array of [Tag](API_dax_Tag.md) objects  
Required: No

## Response Syntax<a name="API_dax_CreateCluster_ResponseSyntax"></a>

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

## Response Elements<a name="API_dax_CreateCluster_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** Cluster **   
A description of the DAX cluster that you have created\.  
Type: [Cluster](API_dax_Cluster.md) object

## Errors<a name="API_dax_CreateCluster_Errors"></a>

For information about the errors that are common to all actions, see [Common Errors](CommonErrors.md)\.

 **ClusterAlreadyExistsFault**   
You already have a DAX cluster with the given identifier\.  
HTTP Status Code: 400

 **ClusterQuotaForCustomerExceededFault**   
You have attempted to exceed the maximum number of DAX clusters for your AWS account\.  
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

 **InvalidParameterGroupStateFault**   
One or more parameters in a parameter group are in an invalid state\.  
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

 **ParameterGroupNotFoundFault**   
The specified parameter group does not exist\.  
HTTP Status Code: 400

 **SubnetGroupNotFoundFault**   
The requested subnet group name does not refer to an existing subnet group\.  
HTTP Status Code: 400

 **TagQuotaPerResourceExceeded**   
You have exceeded the maximum number of tags for this DAX cluster\.  
HTTP Status Code: 400

## See Also<a name="API_dax_CreateCluster_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/dax-2017-04-19/CreateCluster) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/dax-2017-04-19/CreateCluster) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dax-2017-04-19/CreateCluster) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dax-2017-04-19/CreateCluster) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dax-2017-04-19/CreateCluster) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/dax-2017-04-19/CreateCluster) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/dax-2017-04-19/CreateCluster) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/dax-2017-04-19/CreateCluster) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dax-2017-04-19/CreateCluster) 