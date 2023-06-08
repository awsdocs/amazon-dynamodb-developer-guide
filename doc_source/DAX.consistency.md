# DAX and DynamoDB consistency models<a name="DAX.consistency"></a>

Amazon DynamoDB Accelerator \(DAX\) is a write\-through caching service that is designed to simplify the process of adding a cache to DynamoDB tables\. Because DAX operates separately from DynamoDB, it is important that you understand the consistency models of both DAX and DynamoDB to ensure that your applications behave as you expect\.

In many use cases, the way that your application uses DAX affects the consistency of data within the DAX cluster, and the consistency of data between DAX and DynamoDB\.

**Topics**
+ [Consistency among DAX cluster nodes](#DAX.consistency.nodes)
+ [DAX item cache behavior](#DAX.consistency.item-cache)
+ [DAX query cache behavior](#DAX.consistency.query-cache)
+ [Strongly consistent and transactional reads](#DAX.consistency.strongly-consistent-reads)
+ [Negative caching](#DAX.consistency.negative-caching)
+ [Strategies for writes](#DAX.consistency.strategies-for-writes)

## Consistency among DAX cluster nodes<a name="DAX.consistency.nodes"></a>

To achieve high availability for your application, we recommend that you provision your DAX cluster with at least three nodes\. Then place those nodes in multiple Availability Zones within a Region\.

When your DAX cluster is running, it replicates the data among all of the nodes in the cluster \(assuming that you provisioned more than one node\)\. Consider an application that performs a successful `UpdateItem` using DAX\. This action causes the item cache in the primary node to be modified with the new value\. That value is then replicated to all the other nodes in the cluster\. This replication is eventually consistent and usually takes less than one second to complete\.

In this scenario, it's possible for two clients to read the same key from the same DAX cluster but receive different values, depending on the node that each client accessed\. The nodes are all consistent when the update has been fully replicated throughout all the nodes in the cluster\. \(This behavior is similar to the eventually consistent nature of DynamoDB\.\)

If you are building an application that uses DAX, that application should be designed so that it can tolerate eventually consistent data\. 

## DAX item cache behavior<a name="DAX.consistency.item-cache"></a>

Every DAX cluster has two distinct caches—an *item* cache and a *query* cache\. For more information, see [DAX: How it works](DAX.concepts.md)\. 

This section addresses the consistency implications of reading from and writing to the DAX item cache\.

### Consistency of reads<a name="DAX.consistency.item-cache.reads"></a>

With DynamoDB, the `GetItem` operation performs an eventually consistent read by default\. Suppose that you use `UpdateItem` with the DynamoDB client\. If you then try to read the same item immediately afterward, you might see the data as it appeared before the update\. This is due to propagation delay across all the DynamoDB storage locations\. Consistency is usually reached within seconds\. So if you retry the read, you will likely see the updated item\.

When you use `GetItem` with the DAX client, the operation \(in this case, an eventually consistent read\) proceeds as shown following\.

![\[Workflow diagram showing the numbered steps for updating an item.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/dax-item-cache.png)![\[Workflow diagram showing the numbered steps for updating an item.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Workflow diagram showing the numbered steps for updating an item.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

1. The DAX client issues a `GetItem` request\. DAX tries to read the requested item from the item cache\. If the item is in the cache \(*cache hit*\), DAX returns it to the application\.

1. If the item is not available \(*cache miss*\), DAX performs an eventually consistent `GetItem` operation against DynamoDB\.

1. DynamoDB returns the requested item, and DAX stores it in the item cache\.

1. DAX returns the item to the application\.

1. \(Not shown\) If the DAX cluster contains more than one node, the item is replicated to all the other nodes in the cluster\.

The item remains in the DAX item cache, subject to the Time to Live \(TTL\) setting and the least recently used \(LRU\) algorithm for the cache\. For more information, see [DAX: How it works](DAX.concepts.md)\. 

However, during this period, DAX doesn't re\-read the item from DynamoDB\. If someone else updates the item using a DynamoDB client, bypassing DAX entirely, a `GetItem` request using the DAX client yields different results from the same `GetItem` request using the DynamoDB client\. In this scenario, DAX and DynamoDB hold inconsistent values for the same key until the TTL for the DAX item expires\.

If an application modifies data in an underlying DynamoDB table, bypassing DAX, the application needs to anticipate and tolerate data inconsistencies that might arise\.

**Note**  
In addition to `GetItem`, the DAX client also supports `BatchGetItem` requests\. `BatchGetItem` is essentially a wrapper around one or more `GetItem` requests, so DAX treats each of these as an individual `GetItem` operation\.

### Consistency of writes<a name="DAX.consistency.item-cache.writes"></a>

DAX is a write\-through cache, which simplifies the process of keeping the DAX item cache consistent with the underlying DynamoDB tables\.

The DAX client supports the same write API operations as DynamoDB \(`PutItem`, `UpdateItem`, `DeleteItem`, `BatchWriteItem`, and `TransactWriteItems`\)\. When you use these operations with the DAX client, the items are modified in both DAX and DynamoDB\. DAX updates the items in its item cache, regardless of the TTL value for these items\.

For example, suppose that you issue a `GetItem` request from the DAX client to read an item from the `ProductCatalog` table\. \(The partition key is `Id`, and there is no sort key\.\) You retrieve the item whose `Id` is `101`\. The `QuantityOnHand` value for that item is `42`\. DAX stores the item in its item cache with a specific TTL\. For this example, assume that the TTL is 10 minutes\. Then, 3 minutes later, another application uses the DAX client to update the same item so that its `QuantityOnHand` value is now `41`\. Assuming that the item is not updated again, any subsequent reads of the same item during the next 10 minutes return the cached value for `QuantityOnHand` \(`41`\)\.

#### How DAX processes writes<a name="DAX.consistency.item-cache.write-consistency.processing-writes"></a>

DAX is intended for applications that require high\-performance reads\. As a write\-through cache, DAX passes your writes through to DynamoDB synchronously, then automatically and asynchronously replicates resulting updates to your item cache across all nodes in the cluster\. You don't need to manage cache invalidation logic because DAX handles it for you\.

DAX supports the following write operations: `PutItem`, `UpdateItem`, `DeleteItem`, `BatchWriteItem`, and `TransactWriteItems`\.

When you send a `PutItem`, `UpdateItem`, `DeleteItem`, or `BatchWriteItem` request to DAX, the following occurs:
+ DAX sends the request to DynamoDB\.
+ DynamoDB replies to DAX, confirming that the write succeeded\.
+ DAX writes the item to its item cache\.
+ DAX returns success to the requester\.

When you send a `TransactWriteItems` request to DAX, the following occurs:
+ DAX sends the request to DynamoDB\.
+ DynamoDB replies to DAX, confirming that the transaction completed\.
+ DAX returns success to the requester\.
+ In the background, DAX makes a `TransactGetItems` request for each item in the `TransactWriteItems` request to store the item in the item cache\. `TransactGetItems` is used to ensure [serializable isolation](transaction-apis.md#transaction-isolation-serializable)\.

If a write to DynamoDB fails for any reason, including throttling, the item is not cached in DAX\. The exception for the failure is returned to the requester\. This ensures that data is not written to the DAX cache unless it is first written successfully to DynamoDB\.

**Note**  
Every write to DAX alters the state of the item cache\. However, writes to the item cache don't affect the query cache\. \(The DAX item cache and query cache serve different purposes, and operate independently from one another\.\)

## DAX query cache behavior<a name="DAX.consistency.query-cache"></a>

DAX caches the results from `Query` and `Scan` requests in its query cache\. However, these results don't affect the item cache at all\. When your application issues a `Query` or `Scan` request with DAX, the result set is saved in the query cache—not in the item cache\. You can't "warm up" the item cache by performing a `Scan` operation because the item cache and query cache are separate entities\.

### Consistency of query\-update\-query<a name="DAX.consistency.query-cache.read-consistency"></a>

Updates to the item cache, or to the underlying DynamoDB table, do not invalidate or modify the results stored in the query cache\.

To illustrate, consider the following scenario\. An application is working with the `DocumentRevisions` table, which has `DocId` as its partition key and `RevisionNumber` as its sort key\.

1. A client issues a `Query` for `DocId` `101`, for all items with `RevisionNumber` greater than or equal to `5`\. DAX stores the result set in the query cache and returns the result set to the user\.

1. The client issues a `PutItem` request for `DocId` `101` with a `RevisionNumber` value of `20`\.

1. The client issues the same `Query` as described in step 1 \(`DocId` `101` and `RevisionNumber` >= `5`\)\.

In this scenario, the cached result set for the `Query` issued in step 3 is identical to the result set that was cached in step 1\. The reason is that DAX does not invalidate `Query` or `Scan` result sets based on updates to individual items\. The `PutItem` operation from step 2 is only reflected in the DAX query cache when the TTL for the `Query` expires\.

Your application should consider the TTL value for the query cache and how long your application can tolerate inconsistent results between the query cache and the item cache\.

## Strongly consistent and transactional reads<a name="DAX.consistency.strongly-consistent-reads"></a>

To perform a strongly consistent `GetItem`, `BatchGetItem`, `Query`, or `Scan` request, you set the `ConsistentRead` parameter to true\. DAX passes strongly consistent read requests to DynamoDB\. When it receives a response from DynamoDB, DAX returns the results to the client, but it does not cache the results\. DAX can't serve strongly consistent reads by itself because it's not tightly coupled to DynamoDB\. For this reason, any subsequent reads from DAX would have to be eventually consistent reads\. And any subsequent strongly consistent reads would have to be passed through to DynamoDB\.

DAX handles `TransactGetItems` requests the same way it handles strongly consistent reads\. DAX passes all `TransactGetItems` requests to DynamoDB\. When it receives a response from DynamoDB, DAX returns the results to the client, but it doesn't cache the results\.

## Negative caching<a name="DAX.consistency.negative-caching"></a>

DAX supports negative cache entries in both the item cache and the query cache\. A *negative cache entry* occurs when DAX can't find requested items in an underlying DynamoDB table\. Instead of generating an error, DAX caches an empty result and returns that result to the user\.

For example, suppose that an application sends a `GetItem` request to a DAX cluster, and that there is no matching item in the DAX item cache\. This causes DAX to read the corresponding item from the underlying DynamoDB table\. If the item doesn't exist in DynamoDB, DAX stores an empty item in its item cache and returns the empty item to the application\. Now suppose that the application sends another `GetItem` request for the same item\. DAX finds the empty item in the item cache and returns it to the application immediately\. It does not consult DynamoDB at all\.

A negative cache entry remains in the DAX item cache until its item TTL has expired, its LRU is invoked, or the item is modified using `PutItem`, `UpdateItem`, or `DeleteItem`\.

The DAX query cache handles negative cache results in a similar way\. If an application performs a `Query` or `Scan`, and the DAX query cache doesn't contain a cached result, DAX sends the request to DynamoDB\. If there are no matching items in the result set, DAX stores an empty result set in the query cache and returns the empty result set to the application\. Subsequent `Query` or `Scan` requests yield the same \(empty\) result set, until the TTL for that result set has expired\.

## Strategies for writes<a name="DAX.consistency.strategies-for-writes"></a>

The write\-through behavior of DAX is appropriate for many application patterns\. However, there are some application patterns where a write\-through model might not be appropriate\.

For applications that are sensitive to latency, writing through DAX incurs an extra network hop\. So a write to DAX is a little slower than a write directly to DynamoDB\. If your application is sensitive to write latency, you can reduce the latency by writing directly to DynamoDB instead\. For more information, see [Write\-around](#DAX.consistency.strategies-for-writes.write-around)\.

For write\-intensive applications \(such as those that perform bulk data loading\), you might not want to write all of the data through DAX because only a small percentage of that data is ever read by the application\. When you write large amounts of data through DAX, it must invoke its LRU algorithm to make room in the cache for the new items to be read\. This diminishes the effectiveness of DAX as a read cache\.

When you write an item to DAX, the item cache state is altered to accommodate the new item\. \(For example, DAX might need to evict older data from the item cache to make room for the new item\.\) The new item remains in the item cache, subject to the cache's LRU algorithm and the TTL setting for the cache\. As long as the item persists in the item cache, DAX doesn't re\-read the item from DynamoDB\. 

### Write\-through<a name="DAX.consistency.strategies-for-writes.write-through"></a>

The DAX item cache implements a write\-through policy\. For more information, see [How DAX processes writes](#DAX.consistency.item-cache.write-consistency.processing-writes)\. 

When you write an item, DAX ensures that the cached item is synchronized with the item as it exists in DynamoDB\. This is helpful for applications that need to re\-read an item immediately after writing it\. However, if other applications write directly to a DynamoDB table, the item in the DAX item cache is no longer in sync with DynamoDB\. 

To illustrate, consider two users \(Alice and Bob\), who are working with the `ProductCatalog` table\. Alice accesses the table using DAX, but Bob bypasses DAX and accesses the table directly in DynamoDB\.

![\[Workflow diagram showing numbered steps for how users Alice and Bob access a table using DAX and DynamoDB.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/dax-consistency-alice-bob.png)![\[Workflow diagram showing numbered steps for how users Alice and Bob access a table using DAX and DynamoDB.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Workflow diagram showing numbered steps for how users Alice and Bob access a table using DAX and DynamoDB.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

1. Alice updates an item in the `ProductCatalog` table\. DAX forwards the request to DynamoDB, and the update succeeds\. DAX then writes the item to its item cache and returns a successful response to Alice\. From that point on, until the item is ultimately evicted from the cache, any user who reads the item from DAX sees the item with Alice's update\.

1. A short time later, Bob updates the same `ProductCatalog` item that Alice wrote\. However, Bob updates the item directly in DynamoDB\. DAX does not automatically refresh its item cache in response to updates via DynamoDB\. Therefore, DAX users don't see Bob's update\.

1. Alice reads the item from DAX again\. The item is in the item cache, so DAX returns it to Alice without accessing the DynamoDB table\.

In this scenario, Alice and Bob see different representations of the same `ProductCatalog` item\. This is the case until DAX evicts the item from the item cache, or until another user updates the same item again using DAX\.

### Write\-around<a name="DAX.consistency.strategies-for-writes.write-around"></a>

If your application needs to write large quantities of data \(such as a bulk data load\), it might make sense to bypass DAX and write the data directly to DynamoDB\. Such a *write\-around* strategy reduces write latency\. However, the item cache doesn't remain in sync with the data in DynamoDB\.

If you decide to use a write\-around strategy, remember that DAX populates its item cache whenever applications use the DAX client to read data\. This can be advantageous in some cases because it ensures that only the most frequently read data is cached \(as opposed to the most frequently written data\)\.

For example, consider a user \(Charlie\) who wants to work with a different table, the `GameScores` table, using DAX\. The partition key for `GameScores` is `UserId`, so all of Charlie's scores would have the same `UserId`\.

![\[Workflow diagram showing the numbered steps for how Charlie works with a DynamoDB table using DAX.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/dax-consistency-charlie.png)![\[Workflow diagram showing the numbered steps for how Charlie works with a DynamoDB table using DAX.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Workflow diagram showing the numbered steps for how Charlie works with a DynamoDB table using DAX.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

1. Charlie wants to retrieve all of his scores, so he sends a `Query` to DAX\. Assuming that this query has not been issued before, DAX forwards the query to DynamoDB for processing\. It stores the results in the DAX query cache, and then returns the results to Charlie\. The result set remains available in the query cache until it is evicted\.

1. Now suppose that Charlie plays the Meteor Blasters game and achieves a high score\. Charlie sends an `UpdateItem` request to DynamoDB, modifying an item in the `GameScores` table\.

1. Finally, Charlie decides to rerun his earlier `Query` to retrieve all of his data from `GameScores`\. Charlie does not see his high score for Meteor Blasters in the results\. This is because the query results come from the query cache, not the item cache\. The two caches are independent from one another, so a change in one cache does not affect the other cache\.

DAX does not refresh result sets in the query cache with the most current data from DynamoDB\. Each result set in the query cache is current as of the time that the `Query` or `Scan` operation was performed\. Thus, Charlie's `Query` results don't reflect his `PutItem` operation\. This is the case until DAX evicts the result set from the query cache\.