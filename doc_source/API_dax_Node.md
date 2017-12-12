# Node<a name="API_dax_Node"></a>

Represents an individual node within a DAX cluster\.

## Contents<a name="API_dax_Node_Contents"></a>

**Note**  
In the following list, the required parameters are described first\.

 **AvailabilityZone**   
The Availability Zone \(AZ\) in which the node has been deployed\.  
Type: String  
Required: No

 **Endpoint**   
The endpoint for the node, consisting of a DNS name and a port number\. Client applications can connect directly to a node endpoint, if desired \(as an alternative to allowing DAX client software to intelligently route requests and responses to nodes in the DAX cluster\.  
Type: [Endpoint](API_dax_Endpoint.md) object  
Required: No

 **NodeCreateTime**   
The date and time \(in UNIX epoch format\) when the node was launched\.  
Type: Timestamp  
Required: No

 **NodeId**   
A system\-generated identifier for the node\.  
Type: String  
Required: No

 **NodeStatus**   
The current status of the node\. For example: `available`\.  
Type: String  
Required: No

 **ParameterGroupStatus**   
The status of the parameter group associated with this node\. For example, `in-sync`\.  
Type: String  
Required: No

## See Also<a name="API_dax_Node_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dax-2017-04-19/Node) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dax-2017-04-19/Node) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dax-2017-04-19/Node) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dax-2017-04-19/Node) 