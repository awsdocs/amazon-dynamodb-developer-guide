# DAX Cluster Components<a name="DAX.concepts.cluster"></a>

A DAX cluster is comprised of AWS infrastructure components\. This section describes these components, and how they work together\.


+ [Nodes](#DAX.concepts.nodes)
+ [Clusters](#DAX.concepts.clusters)
+ [Regions and Availability Zones](#DAX.concepts.regions-and-azs)
+ [Parameter Groups](#DAX.concepts.parameter-groups)
+ [Security Groups](#DAX.concepts.security-groups)
+ [Cluster ARN](#DAX.concepts.cluster-arn)
+ [Cluster Endpoint](#DAX.concepts.cluster-endpoint)
+ [Node Endpoints](#DAX.concepts.node-endpoints)
+ [Subnet Groups](#DAX.concepts.cluster.security)
+ [Events](#DAX.concepts.events)
+ [Maintenance Window](#DAX.concepts.maintenance-window)

## Nodes<a name="DAX.concepts.nodes"></a>

A *node* is the smallest building block of a DAX cluster\. Each node runs an instance of the DAX software, and maintains a single replica of the cached data\.

You can scale your DAX cluster in one of two ways:

+ By adding more nodes to the cluster\. This will increase the overall read throughput of the cluster\.

+ By using a larger node type\. Larger node types provide more capacity and can increase throughput\. \(Note that you must create a new cluster with the new node type\.\) 

Valid node types are as follows:

+ `dax.r3.large`

+ `dax.r3.xlarge`

+ `dax.r3.2xlarge`

+ `dax.r3.4xlarge`

+ `dax.r3.8xlarge`

Every node within a cluster is of the same node type, and runs the same DAX caching software\.

## Clusters<a name="DAX.concepts.clusters"></a>

A *cluster* is a logical grouping of one or more nodes that DAX manages as a unit\. One of the nodes in the cluster is designated as the *primary* node, and the other nodes \(if any\) are *read replicas*\.

The primary node is responsible for the following:

+ Fulfilling application requests for cached data\.

+ Handling write operations to DynamoDB\.

+ Evicting data from the cache, according to the cluster's eviction policy\.

When changes are made to cached data on the primary node, DAX propagates the changes to all of the read replica nodes\.

Read replicas are responsible for the following:

+ Fulfilling application requests for cached data\.

+ Evicting data from the cache, according to the cluster's eviction policy\.

However, unlike the primary node, read replicas do not write to DynamoDB\.

Read replicas serve two additional purposes:

+ **Scalability**\. If you have a large number of application clients that need to access DAX concurrently, you can add more replicas for read\-scaling\. DAX will spread the load evenly across all of the nodes in the cluster\. \(Another way to increase throughput is to use larger cache node types\.\)

+ **High availability**\. In the event of a primary node failure, DAX automatically fails over to a read replica and designates it as the new primary\. If a replica node fails, other nodes in the DAX cluster will still be able to serve requests until the failed node can be recovered\. For maximum fault tolerance, you should deploy read replicas in separate Availability Zones\. This configuration ensures that your DAX cluster can continue to function, even if an entire Availability Zone should become unavailable\. 

A DAX cluster can support up to ten nodes per cluster \(the primary node, plus a maximum of nine read replicas\)\.

**Important**  
A DAX cluster can be deployed with a single node\. While this configuration might be acceptable for development or test workloads, a one\-node cluster is not fault\-tolerant and we do not recommend a one\-node cluster for production use\. If this single node encounters software or hardware errors, the cluster can become unavailable or lose cached data\.  
For production usage, we strongly recommend using DAX with at least three nodes \(the primary node and at least two read replicas\), where the nodes are placed in different Availability Zones\.

## Regions and Availability Zones<a name="DAX.concepts.regions-and-azs"></a>

A DAX cluster in an AWS region can only interact with DynamoDB tables that are in the same region\. For this reason, you need to ensure that you launch your DAX cluster in the correct region\. If you have DynamoDB tables in other regions, you will need to launch DAX clusters in those regions too\.

Each region is designed to be completely isolated from the other regions\. Within each region are multiple Availability Zones\. By launching your nodes in different Availability Zones, you are able to achieve the greatest possible fault tolerance\.

**Important**  
Do not place all of your cluster's nodes in a single Availability Zone\. In this configuration, your DAX cluster will become unavailable in case of an Availability Zone failure\.  
For production usage, we strongly recommend using DAX with at least three nodes \(the primary node and at least two read replicas\), where the nodes are placed in different Availability Zones\.

## Parameter Groups<a name="DAX.concepts.parameter-groups"></a>

*Parameter groups* are used to manage runtime settings for DAX clusters\. DAX has several parameters that you can use to optimize performance \(such as defining a TTL policy for cached data\)\. A parameter group is a named set of parameters that you can apply to a cluster, thereby guaranteeing that all of the nodes in that cluster are configured in exactly the same way\.

## Security Groups<a name="DAX.concepts.security-groups"></a>

A DAX cluster runs in an Amazon VPC environment—a virtual network that is dedicated to your AWS account, and is isolated from other Amazon VPCs\. A *security group* acts as a virtual firewall for your VPC, allowing you to control inbound and outbound network traffic\.

When you launch a cluster in your VPC, you add an ingress rule to your security group to allow incoming network traffic\. The ingress rule specifies the protocol \(TCP\) and port number \(8111\) for your cluster\. After you add this ingress rule to your security group, your applications running within your VPC can access the DAX cluster\.

## Cluster ARN<a name="DAX.concepts.cluster-arn"></a>

Every DAX cluster is assigned an Amazon Resource Identifier \(ARN\)\. The ARN format is:

```
arn:aws:dax:region:accountID:cache/clusterName
```

You use the cluster ARN in an IAM policy to define permissions for DAX API actions\. For more information, see [DAX Access Control](DAX.access-control.md)\.

## Cluster Endpoint<a name="DAX.concepts.cluster-endpoint"></a>

Every DAX cluster provides a *cluster endpoint* for use by your application\. By accessing the cluster using its endpoint, your application does not need to know the host names and port numbers of individual nodes in the cluster\. Your application automatically "knows" all of the nodes in the cluster, even if you add or remove read replicas\.

Here is an example of a cluster endpoint: 

`myDAXcluster.2cmrwl.clustercfg.dax.use1.cache.amazonaws.com:8111`

## Node Endpoints<a name="DAX.concepts.node-endpoints"></a>

Each of the individual nodes in a DAX cluster has its own host name and point number\. Here is an example of a node endpoint: 

`myDAXcluster-a.2cmrwl.clustercfg.dax.use1.cache.amazonaws.com:8111 `

Your application can access a node directly, using its endpoint; however, we recommend that you treat the DAX cluster as a single unit, and access it using the cluster endpoint instead\. The cluster endpoint insulates your application from having to maintain a list of nodes, and keeping that list up to date when you add or remove nodes from the cluster\.

## Subnet Groups<a name="DAX.concepts.cluster.security"></a>

Access to DAX cluster nodes is restricted to applications running on Amazon EC2 instances within an Amazon Virtual Private Cloud \(Amazon VPC\) environment\. You can use *subnet groups* to grant cluster access from Amazon EC2 instances running on specific subnets\. A subnet group is a collection of subnets \(typically private\) that you can designate for your clusters running in an Amazon Virtual Private Cloud \(VPC\) environment\. 

When you create a DAX cluster, you must specify a subnet group\. DAX uses that subnet group to select a subnet and IP addresses within that subnet to associate with your nodes\.

## Events<a name="DAX.concepts.events"></a>

DAX records significant events within your clusters, such as a failure to add a node, success in adding a node, or changes to security groups\. By monitoring key events, you can know the current state of your clusters and, depending upon the event, be able to take corrective action\. You can access these events using the AWS Management Console, or the `DescribeEvents` action in the DAX management API\.

You can also request that notifications be sent to a specific Amazon SNS topic, so that you will know immediately when an event occurs in your DAX cluster\.

## Maintenance Window<a name="DAX.concepts.maintenance-window"></a>

Every cluster has a weekly maintenance window during which any system changes are applied\. If you don't specify a preferred maintenance window when you create or modify a cache cluster, DAX assigns a 60\-minute maintenance window on a randomly selected day of the week\.

The 60\-minute maintenance window is selected at random from an 8\-hour block of time per region\. The following table lists the time blocks for each region from which the default maintenance windows are assigned\.


****  

| Region Code | Region Name | Maintenance Window | 
| --- | --- | --- | 
|  ap\-northeast\-1  |  Asia Pacific \(Tokyo\) Region  |  13:00–21:00 UTC  | 
|  eu\-west\-1  |  EU \(Ireland\) Region  |  22:00–06:00 UTC  | 
|  us\-east\-1  |  US East \(N\. Virginia\) Region  |  03:00–11:00 UTC  | 
|  us\-west\-1  |  US West \(N\. California\) Region  |  06:00–14:00 UTC  | 
|  us\-west\-2  |  US West \(Oregon\) Region  |  06:00–14:00 UTC  | 

The maintenance window should fall at the time of lowest usage and thus might need modification from time to time\. You can specify a time range of up to 24 hours in duration during which any maintenance activities you have requested should occur\.