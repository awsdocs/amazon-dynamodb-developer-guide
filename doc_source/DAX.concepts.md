# Concepts<a name="DAX.concepts"></a>

DAX is designed to run within an Amazon Virtual Private Cloud environment \(Amazon VPC\)\. The Amazon VPC service defines a virtual network that closely resembles a traditional data center\. With an Amazon VPC, you have control over its IP address range, subnets, routing tables, network gateways, and security settings\. You can launch a DAX cluster in your virtual network, and control access to the cluster by using Amazon VPC security groups\.

**Note**  
If you created your AWS account after 2013\-12\-04, then you already have a default VPC in each AWS region\. A default VPC is ready for you to use—you can immediately start using your default VPC without having to perform any additional configuration steps\.  
For more information, see [Your Default VPC and Subnets](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/default-vpc.html) in the Amazon VPC User Guide\.

The following diagram shows a high\-level overview of DAX:

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/dax_high_level.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

To create a DAX cluster, you use the AWS Management Console\. Unless you specify otherwise, your DAX cluster will run within your default VPC\.

To run your application, you launch an Amazon EC2 instance into your Amazon VPC, and then deploy your application \(with the DAX client\) on the EC2 instance\. At runtime, the DAX client directs all of your application's DynamoDB API requests to the DAX cluster\. If DAX can process one of these API requests directly, it does so; otherwise, it passes the request through to DynamoDB\. Finally, the DAX cluster returns the results to your application\.

## How DAX Processes Requests<a name="DAX.concepts.request-processing"></a>

A DAX cluster consists of one or more nodes\. Each node runs its own instance of the DAX caching software\. One of the nodes serves as the primary node for the cluster\. Additional nodes \(if present\) serve as read replicas\. For more information, see [Nodes](DAX.concepts.cluster.md#DAX.concepts.nodes)\.

Your application can access DAX by specifying the endpoint for the DAX cluster\. The DAX client software works with the cluster endpoint to perform intelligent load\-balancing and routing, so that incoming requests are evenly distributed across all of the nodes in the cluster\.

### Read Operations<a name="w3ab1c27c15c14b6"></a>

DAX can respond to the following API calls:
+ `GetItem`
+ `BatchGetItem`
+ `Query`
+ `Scan`

If the request specifies *eventually consistent reads* \(the default behavior\), it attempts to read the item from DAX:
+ If DAX has the item available \(a *cache hit*\), DAX returns the item to the application without accessing DynamoDB\.
+ If DAX does not have the item available \(a *cache miss*\), DAX passes the request through to DynamoDB\. When it receives the response from DynamoDB, DAX returns the results to the application—but it also writes the results to the cache on the primary node\.

**Note**  
If there are any read replicas in the cluster, DAX automatically keeps the replicas in sync with the primary node\. For more information, see [Clusters](DAX.concepts.cluster.md#DAX.concepts.clusters)\.

If the request specifies *strongly consistent reads*, DAX passes the request through to DynamoDB\. The results from DynamoDB are not cached in DAX; instead, they are simply returned to the application\.

### Write Operations<a name="w3ab1c27c15c14b8"></a>

The following DAX API operations are considered "write\-through":
+ `BatchWriteItem`
+ `UpdateItem`
+ `DeleteItem`
+ `PutItem`

With these operations, data is first written to the DynamoDB table, and then to the DAX cluster\. The operation is successful only if the data is successfully written to *both* the table and to DAX\.

### Other Operations<a name="w3ab1c27c15c14c10"></a>

DAX does not recognize any DynamoDB operations for managing tables \(such as `CreateTable`, `UpdateTable`, and so on\)\. If your application needs to perform these operations, it will need to access DynamoDB directly rather than using DAX\.

## Item Cache<a name="DAX.concepts.item-cache"></a>

DAX maintains an *item cache* to store the results from `GetItem` and `BatchGetItem` operations\. The items in the cache represent eventually consistent data from DynamoDB, and are stored by their primary key values\.

When an application sends a `GetItem` or `BatchGetItem` request, DAX attempts to read the items directly from the item cache using the specified key values\. If the items are found \(cache hit\), DAX returns them to the application immediately\. If the items are not found \(cache miss\), DAX sends the request to DynamoDB\. DynamoDB processes the requests using eventually consistent reads, and returns the items to DAX\. DAX stores them in the item cache, and then returns them to the application\.

The item cache has a time\-to\-live setting \(TTL\), which is 5 minutes by default\. DAX assigns a timestamp to every item that it writes to the item cache\. An item expires if it has remained in the cache for longer than the TTL setting\. If you issue a `GetItem` request on an expired item, this is considered a cache miss, so DAX will send the `GetItem` request to DynamoDB\.

**Note**  
You can specify the TTL setting for the item cache when you create a new DAX cluster\. For more information, see [Managing DAX Clusters](DAX.cluster-management.md)\.\)

DAX also maintains a least recently used list \(LRU\) for the item cache\. The LRU list keeps track of when an item was first written to the cache, and when the item was last read from the cache\. If the item cache becomes full, DAX will evict older items \(even if they have not expired yet\) to make room for new items\. The LRU algorithm is always enabled for the item cache, and is not user\-configurable\.

## Query Cache<a name="DAX.concepts.query-cache"></a>

DAX also maintains a *query cache* to store the results from `Query` and `Scan` operations\. The items in this cache represent result sets from queries and scans on DynamoDB tables\. These result sets are stored by their parameter values\.

When an application sends a `Query` or `Scan` request, DAX attempts to read a matching result set from the query cache using the specified parameter values\. If the result set is found \(cache hit\), DAX returns it to the application immediately\. If the result set is not found \(cache miss\), DAX sends the request to DynamoDB\. DynamoDB processes the requests using eventually consistent reads, and returns the result set to DAX\. DAX stores it in the item cache, and then returns it to the application\.

**Note**  
You can specify the TTL setting for the query cache when you create a new DAX cluster\. For more information, see [Managing DAX Clusters](DAX.cluster-management.md)\.\)

DAX also maintains a least recently used list \(LRU\) for the query cache\. The LRU list keeps track of when a result set was first written to the cache, and when the result was last read from the cache\. If the query cache becomes full, DAX will evict older result sets \(even if they have not expired yet\) to make room for new result sets\. The LRU algorithm is always enabled for the query cache, and is not user\-configurable\.