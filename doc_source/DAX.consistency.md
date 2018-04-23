# DAX and DynamoDB Consistency Models<a name="DAX.consistency"></a>

DAX is a write\-through caching service, designed to simplify the process of adding a cache to Amazon DynamoDB tables\. Because DAX operates separately from DynamoDB, it is important that you understand the consistency models of both DAX and DynamoDB to ensure that your application behaves as you expect\.

In many use cases, the way that your application uses DAX will affect the consistency of data within the DAX cluster, as well as the consistency of data between DAX and DynamoDB\.

**Topics**
+ [Consistency Among DAX Cluster Nodes](#DAX.consistency.nodes)
+ [DAX Item Cache Behavior](#DAX.consistency.item-cache)
+ [DAX Query Cache Behavior](#DAX.consistency.query-cache)
+ [Strongly Consistent Reads](#DAX.consistency.strongly-consistent-reads)
+ [Negative Caching](#DAX.consistency.negative-caching)
+ [Strategies for Writes](#DAX.consistency.strategies-for-writes)

## Consistency Among DAX Cluster Nodes<a name="DAX.consistency.nodes"></a>

To achieve high availability for your application, we recommend that you provision your DAX cluster with at least two nodes \(ideally three or more\), and place those nodes in multiple availability zones within a region\.

When your DAX cluster is running, it will replicate the data among all of the nodes in the cluster \(assuming that you have provisioned more than one node\)\. Consider an application that performs a successful `UpdateItem` using DAX\. This causes the item cache in the primary node to be modified with the new value; that value will then be replicated to all of the other nodes in the cluster\. This replication is eventually consistent, and usually takes less than one second to complete\.

In this scenario, it is possible for two clients to read the same key from the same DAX cluster but receive different values, depending on the node that each client accessed\. The nodes will all be consistent when the update has been fully replicated throughout all of the nodes in the cluster\. \(Note that this behavior is similar to the eventually consistent nature of DynamoDB\.\)

If you are building an application that uses DAX, that application should be designed in such a way that it can tolerate eventually consistent data\. 

## DAX Item Cache Behavior<a name="DAX.consistency.item-cache"></a>

Every DAX cluster has two distinct caches—an item cache and a query cache\. \(For more information, see [Concepts](DAX.concepts.md)\.\) This section addresses the consistency implications of reading from and writing to the DAX item cache\.

### Consistency of Reads<a name="DAX.consistency.item-cache.reads"></a>

With Amazon DynamoDB, the `GetItem` operation performs an eventually consistent read by default\. If you use `UpdateItem` with the DynamoDB client, and then attempt to read the same item immediately afterward, you might see the data as it appeared prior to the update\. This is due to propagation delay across all of the DynamoDB storage locations\. Consistency is usually reached within seconds, so if you retry the read, you will likely see the updated item\.

When you use `GetItem` with the DAX client, the operation proceeds as follows:

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/dax-item-cache.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

1. The DAX client issues a `GetItem` request\. DAX attempts to read the requested item from the item cache\. If the item is in the cache \(cache hit\), DAX returns it to the application\.

1. If the item is not available \(cache miss\), DAX performs an eventually consistent `GetItem` operation against DynamoDB\.

1. DynamoDB returns the requested item, and DAX stores it in the item cache\.

1. DAX returns the item to the application\.

1. \(not shown\) If the DAX cluster contains more than one node, the item is replicated to all of the other nodes in the cluster\.

The item will remain in the DAX item cache, subject to the TTL setting and LRU algorithm for the cache \(see [Concepts](DAX.concepts.md)\)\. However, during this period, DAX will not re\-read the item from DynamoDB\. If someone else updates the item using a DynamoDB client, bypassing DAX entirely, then a `GetItem` request using the DAX client will yield different results from the same `GetItem` request using the DynamoDB client\. In this scenario, DAX and DynamoDB will hold inconsistent values for the same key until the TTL for the DAX item has expired\.

If an application modifies data in an underlying DynamoDB table, bypassing DAX, the application will need to anticipate and tolerate data inconsistencies that might arise\.

**Note**  
In addition to `GetItem`, the DAX client also supports `BatchGetItem` requests\. `BatchGetItem` is essentially a wrapper around one or more `GetItem` requests, so DAX treats each of these as an individual `GetItem` operation\.

### Consistency of Writes<a name="DAX.consistency.item-cache.writes"></a>

DAX is a write\-through cache, which simplifies the process of keeping the DAX item cache consistent with the underlying DynamoDB tables\.

The DAX client supports the same write API operations as DynamoDB \(`PutItem`, `UpdateItem`, `DeleteItem`, and `BatchWriteItem`\)\. When you use these operations with the DAX client, the items are modified in both DAX and DynamoDB\. DAX will update the items in its item cache, regardless of the TTL value for these items\.

For example, suppose you issue a `GetItem` request from the DAX client to read an item from the *ProductCatalog* table\. \(The partition key is *Id*; there is no sort key\.\) You retrieve the item whose *Id* is `101`; the *QuantityOnHand* value for that item is `42`\. DAX stores the item in its item cache with a specific TTL; for this example, let us assume that the TTL is ten minutes\. Three minutes later, another application uses the DAX client to update the same item, so that its *QuantityOnHand* value is now `41`\. Assuming that the item is not updated again, any subsequent reads of the same item during the next ten minutes will return the cached value for *QuantityOnHand* \(`41`\)\.

#### How DAX Processes Writes<a name="DAX.consistency.item-cache.write-consistency.processing-writes"></a>

DAX is intended for applications that require high\-performance reads\. As a write\-through cache, DAX allows you to issue writes directly, so that your writes are immediately reflected in the item cache\. You do not need to manage cache invalidation logic, because DAX handles it for you\.

DAX supports the following write operations: `PutItem`, `UpdateItem`, `DeleteItem`, and `BatchWriteItem`\. When you send one of these requests to DAX, it does the following:
+ DAX sends the request to DynamoDB\.
+ DynamoDB replies to DAX, confirming that the write succeeded\.
+ DAX writes the item to its item cache\.
+ DAX returns success to the requester\.

If a write to DynamoDB fails for any reason, including throttling, then the item will not be cached in DAX and the exception for the failure will be returned to the requester\. This ensures that data is not written to the DAX cache unless it is first written successfully to DynamoDB\.

**Note**  
Every write to DAX alters the state of the item cache; however, writes to the item cache do not affect the query cache\. \(The DAX item cache and query cache serve different purposes, and operate independently from one another\.\)

## DAX Query Cache Behavior<a name="DAX.consistency.query-cache"></a>

DAX caches the results from `Query` and `Scan` requests in its query cache; however, these results do not affect the item cache at all\. When your application issues a `Query` or `Scan` request with DAX, the result set is saved in the query cache—not in the item cache\. You cannot "warm up" the item cache by performing a `Scan` operation, because the item cache and query cache are separate entities\.

### Consistency of Query\-Update\-Query<a name="DAX.consistency.query-cache.read-consistency"></a>

Updates to the item cache, or to the underlying DynamoDB table, do not invalidate or modify the results stored in the query cache\.

To illustrate, consider the following scenario where an application is working with a table named *DocumentRevisions*, which has *DocId* as its partition key and *RevisionNumber* as its sort key\.

1. A client issues a `Query` for *DocId* `101`, for all items with *RevisionNumber* is greater than or equal to `5`\. DAX stores the result set in the query cache, and returns the result set to the user\.

1. The client issues a `PutItem` request for *DocId* `101` with a *RevisionNumber* value of 20\.

1. The client issues the same `Query` as described in step 1 \(*DocId* `101` and *RevisionNumber* >= `5`\)\.

In this scenario, the cached result set for the `Query` issued in step 3 will be identical to the result set that was cached in step 1\. The reason is that DAX does not invalidate `Query` or `Scan` result sets based upon updates to individual items\. The `PutItem` operation from step 2 will only be reflected in the DAX query cache when the TTL for the `Query` expires\.

Your application should consider the TTL value for the query cache, and how long your application is able to tolerate inconsistent results between the query cache and the item cache\.

## Strongly Consistent Reads<a name="DAX.consistency.strongly-consistent-reads"></a>

To perform a strongly consistent read request, you set the `ConsistentRead` parameter to true\. DAX passes strongly consistent read requests to DynamoDB\. When it receives a response from DynamoDB, DAX returns the results to the client, but it does not cache the results\. DAX cannot serve strongly consistent reads by itself, because it is not tightly coupled to DynamoDB\. For this reason, any subsequent reads from DAX would have to be eventually consistent reads, and any subsequent strongly consistent reads would have to be passed through to DynamoDB\.

## Negative Caching<a name="DAX.consistency.negative-caching"></a>

DAX supports negative cache entries, in both the item cache and the query cache\. A *negative cache entry* occurs when DAX cannot find requested items in an underlying DynamoDB table\. Instead of generating an error, DAX caches an empty result and returns that result to the user\.

For example, suppose that an application sends a `GetItem` request to a DAX cluster, and that there is no matching item in the DAX item cache\. This will cause DAX to read the corresponding item from the underlying DynamoDB table\. If the item does not exist in DynamoDB, then DAX will store an empty item in its item cache, and then return the empty item to the application\. Now suppose the application sends another `GetItem` request for the same item\. DAX will find the empty item in the item cache, and return it to the application immediately\. It does not consult DynamoDB at all\.

A negative cache entry will remain in the DAX item cache until its item TTL has expired, LRU is invoked, or until the item is modified using `PutItem`, `UpdateItem` or `DeleteItem`\.

The DAX query cache handles negative cache results in a similar way\. If an application performs a `Query` or `Scan`, and the DAX query cache does not contain a cached result, then DAX sends the request to DynamoDB\. If there are no matching items in the result set, then DAX stores an empty result set in the query cache, and returns the empty result set to the application\. Subsequent `Query` or `Scan` requests will yield the same \(empty\) result set, until the TTL for that result set has expired\.

## Strategies for Writes<a name="DAX.consistency.strategies-for-writes"></a>

The write\-through behavior of DAX is appropriate for many application patterns\. However, there are some application patterns where a write\-through model might not be appropriate\.

For applications that are sensitive to latency, writing through DAX incurs an extra network hop, so a write to DAX will be a little slower than a write directly to DynamoDB\. If your application is sensitive to write latency, you can reduce the latency by writing directly to DynamoDB instead\. \(For more information, see [Write\-Around](#DAX.consistency.strategies-for-writes.write-around)\.\)

For write\-intensive applications \(such as those that perform bulk data loading\), it might not be desirable to write all of the data through DAX because only a very small percentage of that data is ever read by the application\. When you write large amounts of data through DAX, it must invoke its LRU algorithm to make room in the cache for the new items to be read\. This diminishes the effectiveness of DAX as a read cache\.

When you write an item to DAX, the item cache state is altered to accommodate the new item\. \(For example, DAX might need to evict older data from the item cache to make room for the new item\.\) The new item remains in the item cache, subject to the cache's LRU algorithm and the TTL setting for the cache\. As long as the item persists in the item cache, DAX will not re\-read the item from DynamoDB\. 

### Write\-Through<a name="DAX.consistency.strategies-for-writes.write-through"></a>

The DAX item cache implements a write\-through policy \(see [How DAX Processes Writes](#DAX.consistency.item-cache.write-consistency.processing-writes)\)\. When you write an item, DAX ensures that the cached item is synchronized with the item as it exists in DynamoDB\. This is helpful for applications that need to re\-read an item immediately after writing it\. However, if other applications write directly to a DynamoDB table, the item in the DAX item cache will no longer be in sync with DynamoDB\. 

To illustrate, consider two users \(Alice and Bob\) who are working with the *ProductCatalog* table\. Alice accesses the table using DAX, but Bob bypasses DAX and accesses the table directly in DynamoDB\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/dax-consistency-alice-bob.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

1. Alice updates an item in the *ProductCatalog* table\. DAX forwards the request to DynamoDB, and the update succeeds\. DAX then writes the item to its item cache, and returns a successful response to Alice\. From that point on, until the item is ultimately evicted from the cache, any user who reads the item from DAX will see the item with Alice's update\.

1. A short time later, Bob updates the same *ProductCatalog* item that Alice wrote—however, Bob updates the item directly in DynamoDB\. DAX does not automatically refresh its item cache in response to updates via DynamoDB; therefore, DAX users do not see Bob's update\.

1. Alice reads the item from DAX again\. The item is in the item cache, so DAX returns it to Alice without accessing the DynamoDB table\.

In this scenario, Alice and Bob will see different representations of the same *ProductCatalog* item\. This will be the case until DAX evicts the item from the item cache, or until another user updates the same item again using DAX\.

### Write\-Around<a name="DAX.consistency.strategies-for-writes.write-around"></a>

If your application needs to write large quantities of data \(such as a bulk data load\), it might make sense to bypass DAX and write the data directly to DynamoDB\. Such a *write\-around* strategy will reduce write latency; however, the item cache will not remain in sync with the data in DynamoDB\.

If you decide to use a write\-around strategy, remember that DAX will populate its item cache whenever applications use the DAX client to read data\. This can be advantageous in some cases, because it ensures that only the most frequently\-read data is cached \(as opposed to the most\-frequently written data\)\.

Consider a user \(Charlie\) who wants to work with the *GameScores* table using DAX\. The partition key for *GameScores* is `UserId`, so all of Charlie's scores would have the same `UserId`\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/dax-consistency-charlie.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

1. Charlie wants to retrieve all of his scores, so he sends a `Query` to DAX\. Assuming that this query has not been issued before, DAX forwards the query to DynamoDB for processing, stores the results in the DAX query cache, and then returns the results to Charlie\. The result set will remain available in the query cache until it is evicted\.

1. Now suppose that Charlie plays the Meteor Blasters game and achieves a high score\. Charlie sends an `UpdateItem` request to DynamoDB, modifying an item in the *GameScores* table\.

1. Finally, Charlie decides to rerun his earlier `Query` to retrieve all of his data from *GameScores*\. Charlie does not see his high score for Meteor Blasters in the results\. This is because the query results come from the query cache, not the item cache\. \(The two caches are independent from one another, so a change in one cache does not affect the other cache\.\)

DAX does not refresh result sets in the query cache with the most current data from DynamoDB\. Each result set in the query cache is current as of the time that the `Query` or `Scan` operation was performed\. Thus, Charlie's `Query` results do not reflect his `PutItem` operation\. This will be the case until DAX evicts the result set from the query cache\.