# Cluster<a name="API_dax_Cluster"></a>

Contains all of the attributes of a specific DAX cluster\.

## Contents<a name="API_dax_Cluster_Contents"></a>

**Note**  
In the following list, the required parameters are described first\.

 **ActiveNodes**   
The number of nodes in the cluster that are active \(i\.e\., capable of serving requests\)\.  
Type: Integer  
Required: No

 **ClusterArn**   
The Amazon Resource Name \(ARN\) that uniquely identifies the cluster\.   
Type: String  
Required: No

 **ClusterDiscoveryEndpoint**   
The configuration endpoint for this DAX cluster, consisting of a DNS name and a port number\. Client applications can specify this endpoint, rather than an individual node endpoint, and allow the DAX client software to intelligently route requests and responses to nodes in the DAX cluster\.  
Type: [Endpoint](API_dax_Endpoint.md) object  
Required: No

 **ClusterName**   
The name of the DAX cluster\.  
Type: String  
Required: No

 **Description**   
The description of the cluster\.  
Type: String  
Required: No

 **IamRoleArn**   
A valid Amazon Resource Name \(ARN\) that identifies an IAM role\. At runtime, DAX will assume this role and use the role's permissions to access DynamoDB on your behalf\.  
Type: String  
Required: No

 **NodeIdsToRemove**   
A list of nodes to be removed from the cluster\.  
Type: Array of strings  
Required: No

 **Nodes**   
A list of nodes that are currently in the cluster\.  
Type: Array of [Node](API_dax_Node.md) objects  
Required: No

 **NodeType**   
The node type for the nodes in the cluster\. \(All nodes in a DAX cluster are of the same type\.\)  
Type: String  
Required: No

 **NotificationConfiguration**   
Describes a notification topic and its status\. Notification topics are used for publishing DAX events to subscribers using Amazon Simple Notification Service \(SNS\)\.  
Type: [NotificationConfiguration](API_dax_NotificationConfiguration.md) object  
Required: No

 **ParameterGroup**   
The parameter group being used by nodes in the cluster\.  
Type: [ParameterGroupStatus](API_dax_ParameterGroupStatus.md) object  
Required: No

 **PreferredMaintenanceWindow**   
A range of time when maintenance of DAX cluster software will be performed\. For example: `sun:01:00-sun:09:00`\. Cluster maintenance normally takes less than 30 minutes, and is performed automatically within the maintenance window\.  
Type: String  
Required: No

 **SecurityGroups**   
A list of security groups, and the status of each, for the nodes in the cluster\.  
Type: Array of [SecurityGroupMembership](API_dax_SecurityGroupMembership.md) objects  
Required: No

 **Status**   
The current status of the cluster\.  
Type: String  
Required: No

 **SubnetGroup**   
The subnet group where the DAX cluster is running\.  
Type: String  
Required: No

 **TotalNodes**   
The total number of nodes in the cluster\.  
Type: Integer  
Required: No

## See Also<a name="API_dax_Cluster_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dax-2017-04-19/Cluster) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dax-2017-04-19/Cluster) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dax-2017-04-19/Cluster) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dax-2017-04-19/Cluster) 