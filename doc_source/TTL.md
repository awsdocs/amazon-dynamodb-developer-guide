# Expiring items by using DynamoDB Time to Live \(TTL\)<a name="TTL"></a>

Amazon DynamoDB Time to Live \(TTL\) allows you to define a per\-item timestamp to determine when an item is no longer needed\. Shortly after the date and time of the specified timestamp, DynamoDB deletes the item from your table without consuming any write throughput\. TTL is provided at no extra cost as a means to reduce stored data volumes by retaining only the items that remain current for your workload’s needs\. 

TTL is useful if you store items that lose relevance after a specific time\. The following are example TTL use cases:
+ Remove user or sensor data after one year of inactivity in an application\.
+ Archive expired items to an Amazon S3 data lake via Amazon DynamoDB Streams and AWS Lambda\.
+ Retain sensitive data for a certain amount of time according to contractual or regulatory obligations\.

**Note**  
If you are using [Version 2019\.11\.21 \(Current\)](globaltables.V2.md) of global tables and you also use the [Time to Live](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/TTL.html) feature, DynamoDB replicates TTL deletes to all replica tables\. The initial TTL delete does not consume write capacity in the region in which the TTL expiry occurs\. However, the replicated TTL delete to the replica table\(s\) consumes a replicated write capacity unit when using provisioned capacity, or replicated write when using on\-demand capacity mode, in each of the replica regions and applicable charges will apply\.
In [Version 2019\.11\.21 \(Current\)](globaltables.V2.md), when a TTL delete occurs it is replicated to all replica regions\. These replicated writes do not contain `type` or `principalID` properties\. This can make it difficult to distinguish a TTL delete from a user delete in the replicated tables\.

For more information about TTL, see these topics:
+ [Using Time to Live](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/time-to-live-ttl-before-you-start.html)\.
+ [Time to Live: How It Works](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/howitworks-ttl.html)\.
+  [Enabling Time to Live](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/time-to-live-ttl-how-to.html)\.