# DAX: How it works<a name="DAX.concepts"></a>

Amazon DynamoDB Accelerator \(DAX\) is designed to run within an Amazon Virtual Private Cloud \(Amazon VPC\) environment\. The Amazon VPC service defines a virtual network that closely resembles a traditional data center\. With a VPC, you have control over its IP address range, subnets, routing tables, network gateways, and security settings\. You can launch a DAX cluster in your virtual network and control access to the cluster by using Amazon VPC security groups\.

**Note**  
If you created your AWS account after December 4, 2013, you already have a default VPC in each AWS Region\. The VPC is ready for you to use immediately—without having to perform any additional configuration steps\.  
For more information, see [Default VPC and default subnets](https://docs.aws.amazon.com/vpc/latest/userguide/default-vpc.html) in the *Amazon VPC User Guide*\.

The following diagram shows a high\-level overview of DAX\.

![\[Workflow diagram showing interaction of application, DAX client, and DAX cluster in a VPC.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/dax_high_level.png)![\[Workflow diagram showing interaction of application, DAX client, and DAX cluster in a VPC.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Workflow diagram showing interaction of application, DAX client, and DAX cluster in a VPC.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

To create a DAX cluster, you use the AWS Management Console\. Unless you specify otherwise, your DAX cluster runs within your default VPC\. To run your application, you launch an Amazon EC2 instance into your Amazon VPC\. You then deploy your application \(with the DAX client\) on the EC2 instance\.

At runtime, the DAX client directs all of your application's DynamoDB API requests to the DAX cluster\. If DAX can process one of these API requests directly, it does so\. Otherwise, it passes the request through to DynamoDB\. 

Finally, the DAX cluster returns the results to your application\.

**Topics**
+ [How DAX processes requests](#DAX.concepts.request-processing)
+ [Item cache](#DAX.concepts.item-cache)
+ [Query cache](#DAX.concepts.query-cache)

## How DAX processes requests<a name="DAX.concepts.request-processing"></a>

A DAX cluster consists of one or more nodes\. Each node runs its own instance of the DAX caching software\. One of the nodes serves as the primary node for the cluster\. Additional nodes \(if present\) serve as read replicas\. For more information, see [Nodes](DAX.concepts.cluster.md#DAX.concepts.nodes)\.

Your application can access DAX by specifying the endpoint for the DAX cluster\. The DAX client software works with the cluster endpoint to perform intelligent load balancing and routing\.

### Read operations<a name="DAX.concepts.request-processing-read"></a>

DAX can respond to the following API calls:
+ `GetItem`
+ `BatchGetItem`
+ `Query`
+ `Scan`

If the request specifies *eventually consistent reads* \(the default behavior\), it tries to read the item from DAX:
+ If DAX has the item available \(a *cache hit*\), DAX returns the item to the application without accessing DynamoDB\.
+ If DAX does not have the item available \(a *cache miss*\), DAX passes the request through to DynamoDB\. When it receives the response from DynamoDB, DAX returns the results to the application\. But it also writes the results to the cache on the primary node\.

**Note**  
If there are any read replicas in the cluster, DAX automatically keeps the replicas in sync with the primary node\. For more information, see [Clusters](DAX.concepts.cluster.md#DAX.concepts.clusters)\.

If the request specifies *strongly consistent reads*, DAX passes the request through to DynamoDB\. The results from DynamoDB are not cached in DAX\. Instead, they are simply returned to the application\.

### Write operations<a name="DAX.concepts.request-processing-write"></a>

The following DAX API operations are considered "write\-through":
+ `BatchWriteItem`
+ `UpdateItem`
+ `DeleteItem`
+ `PutItem`

With these operations, data is first written to the DynamoDB table, and then to the DAX cluster\. The operation is successful only if the data is successfully written to *both* the table and to DAX\.

### Other operations<a name="DAX.concepts.request-processing-other"></a>

DAX does not recognize any DynamoDB operations for managing tables \(such as `CreateTable`, `UpdateTable`, and so on\)\. If your application needs to perform these operations, it must access DynamoDB directly rather than using DAX\.

For detailed information about DAX and DynamoDB consistency, see [DAX and DynamoDB consistency models](DAX.consistency.md)\.

For information about how transactions work in DAX, see [Using transactional APIs in DynamoDB Accelerator \(DAX\)](transaction-apis.md#transaction-apis-dax)\.

### Request rate limiting<a name="DAX.concepts.request-processing-throttling"></a>

If the number of requests sent to DAX exceeds the capacity of a node, DAX limits the rate at which it accepts additional requests by returning a [ThrottlingException](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/CommonErrors.html#CommonErrors-ThrottlingException)\. DAX continuously evaluates your CPU utilization to determine the volume of requests it can process while maintaining a healthy cluster state\.

You can monitor the [ThrottledRequestCount metric](dax-metrics-dimensions-dax.md) that DAX publishes to Amazon CloudWatch\. If you see these exceptions regularly, you should consider [scaling up your cluster](DAX.cluster-management.md#DAX.cluster-management.scaling)\.

## Item cache<a name="DAX.concepts.item-cache"></a>

DAX maintains an *item cache* to store the results from `GetItem` and `BatchGetItem` operations\. The items in the cache represent eventually consistent data from DynamoDB, and are stored by their primary key values\.

When an application sends a `GetItem` or `BatchGetItem` request, DAX tries to read the items directly from the item cache using the specified key values\. If the items are found \(cache hit\), DAX returns them to the application immediately\. If the items are not found \(cache miss\), DAX sends the request to DynamoDB\. DynamoDB processes the requests using eventually consistent reads and returns the items to DAX\. DAX stores them in the item cache and then returns them to the application\.

The item cache has a Time to Live \(TTL\) setting, which is 5 minutes by default\. DAX assigns a timestamp to every item that it writes to the item cache\. An item expires if it has remained in the cache for longer than the TTL setting\. If you issue a `GetItem` request on an expired item, this is considered a cache miss, and DAX sends the `GetItem` request to DynamoDB\.

**Note**  
You can specify the TTL setting for the item cache when you create a new DAX cluster\. For more information, see [Managing DAX clusters ](DAX.cluster-management.md)\.

DAX also maintains a least recently used \(LRU\) list for the item cache\. The LRU list tracks when an item was first written to the cache, and when the item was last read from the cache\. If the item cache becomes full, DAX evicts older items \(even if they haven't expired yet\) to make room for new items\. The LRU algorithm is always enabled for the item cache and is not user\-configurable\.

If you specify zero as the *item cache* TTL setting, items in the item cache will only be refreshed due to an LRU eviction or a [ "write\-through"](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DAX.concepts.html#DAX.concepts.request-processing-write) operation\.

For detailed information about the consistency of the item cache in DAX, see [DAX item cache behavior](DAX.consistency.md#DAX.consistency.item-cache)\.

## Query cache<a name="DAX.concepts.query-cache"></a>

DAX also maintains a *query cache* to store the results from `Query` and `Scan` operations\. The items in this cache represent result sets from queries and scans on DynamoDB tables\. These result sets are stored by their parameter values\.

When an application sends a `Query` or `Scan` request, DAX tries to read a matching result set from the query cache using the specified parameter values\. If the result set is found \(cache hit\), DAX returns it to the application immediately\. If the result set is not found \(cache miss\), DAX sends the request to DynamoDB\. DynamoDB processes the requests using eventually consistent reads and returns the result set to DAX\. DAX stores it in the query cache and then returns it to the application\.

**Note**  
You can specify the TTL setting for the query cache when you create a new DAX cluster\. For more information, see [Managing DAX clusters ](DAX.cluster-management.md)\.

DAX also maintains an LRU list for the query cache\. The list tracks when a result set was first written to the cache, and when the result was last read from the cache\. If the query cache becomes full, DAX evicts older result sets \(even if they have not expired yet\) to make room for new result sets\. The LRU algorithm is always enabled for the query cache, and is not user\-configurable\.

If you specify zero as the *query cache* TTL setting, the query response will not be cached\.

For detailed information about the consistency of the query cache in DAX, see [DAX query cache behavior](DAX.consistency.md#DAX.consistency.query-cache)\.