# Global tables: How it works<a name="V2globaltables_HowItWorks"></a>


****  

|  | 
| --- |
| There are two versions of DynamoDB global tables available: [Version 2019\.11\.21 \(Current\)](globaltables.V2.md) and [Version 2017\.11\.29 \(Legacy\)](globaltables.V1.md)\. Customers should use version 2019\.11\.21 \(Current\) when possible, as it provides greater flexibility, higher efficiency and consumes less write capacity than 2017\.11\.29 \(Legacy\)\. To determine which version you are using, see [Determining the global table version you are using](globaltables.DetermineVersion.md)\. | 

 The following sections describe the concepts and behavior of global tables in Amazon DynamoDB\. 

## Global table concepts<a name="V2globaltables_HowItWorks.KeyConcepts"></a>

A *global table* is a collection of one or more replica tables, all owned by a single AWS account\.

A *replica table* \(or *replica*, for short\) is a single DynamoDB table that functions as a part of a global table\. Each replica stores the same set of data items\. Any given global table can only have one replica table per AWS Region\. For more information about how to get started with global tables, see [Tutorial: Creating a global table](V2globaltables.tutorial.md)\.

When you create a DynamoDB global table, it consists of multiple replica tables \(one per Region\) that DynamoDB treats as a single unit\. Every replica has the same table name and the same primary key schema\. When an application writes data to a replica table in one Region, DynamoDB propagates the write to the other replica tables in the other AWS Regions automatically\.

You can add replica tables to the global table so that it can be available in additional Regions\. 

With Version 2019\.11\.21 \(Current\), when you create a Global Secondary Index in one Region it is automatically replicated to the other Region\(s\) as well as automatically backfilled\.

## Common tasks<a name="V2globaltables_HowItWorks.CommonTasks"></a>

Common tasks for global tables work as follows\.

You can delete a global table’s replica table the same as a regular table\. This will stop replication to that Region and delete the table copy kept in that Region\. You cannot sever the replication and have copies of the table exist as independent entities\. You can instead copy the global table to a local table in that Region, and then delete the global replica for that Region\. 

**Note**  
You won’t be able to delete a source table until at least 24 hours after it’s used to initiate a new Region\. If you try to delete it too soon you will receive an error\.

Conflicts can arise if applications update the same item in different Regions at about the same time\. To help ensure eventual consistency, DynamoDB global tables use a “last writer wins” method to reconcile between concurrent updates\. All the replicas will agree on the latest update and converge toward a state in which they all have identical data\. 

**Note**  
There are several ways to avoid conflicts, including:  
Only allowing writes to the table in one Region\.
Routing user traffic to different Regions according to your write policies, to ensure there are no conflicts\.
Avoiding the use of non\-idempotent updates such as Bookmark = Bookmark \+ 1, in favor of static updates such as Bookmark=25\.
Keep in mind that when you need to route writes or reads to one Region, it’s up to your application to ensure that flow is enforced\.

## Monitoring global tables<a name="monitoring-global-tables"></a>

You can use CloudWatch to observe the metric `ReplicationLatency`\. This tracks the elapsed time between when an item is written to a replica table, and when that item appears in another replica in the global table\. It’s expressed in milliseconds and is emitted for every source\-Region and destination\-Region pair\. This metric is kept at the source Region\. This is the only CloudWatch metric provided by Global Tables v2\. 

The latencies you will observe depend on the distance between your chosen Regions, as well as other variables\. Latencies in the 0\.5 to 2\.5 second range for Regions can be common within the same geographic area\.

## Time To Live \(TTL\)<a name="global-tables-ttl"></a>

You can use Time To Live \(TTL\) to specify an attribute name whose value indicates the time of expiration for the item\. This value is given as a number in seconds since the start of the Unix epoch\. After that time DynamoDB can delete the item without incurring write costs\.

With global tables you configure TTL in one Region, and that setting is auto replicated to the other Region\(s\)\. When an item is deleted via a TTL rule, that work is performed without consuming Write Units on the source table \- but the target table\(s\) will incur Replicated Write Unit costs\. 

Be aware that if the source and target table have very low Provisioned write capacity, this may cause throttling as the TTL deletes require write capacity\.

## Streams and transactions with global tables<a name="global-tables-streams"></a>

Each global table produces an independent stream based on all its writes, regardless of the origination point for those writes\. You can choose to consume this DynamoDB stream in one Region or in all Regions independently\. 

If you want processed local writes but not replicated writes, you can add your own region attribute to each item\. Then you can use a Lambda event filter to invoke only the Lambda for writes in the local Region\.

Transactional operations provide ACID \(Atomicity, Consistency, Isolation, and Durability\) guarantees ONLY within the Region where the write is made originally\. Transactions are not supported across Regions in global tables\.

For example, if you have a global table with replicas in the US East \(Ohio\) and US West \(Oregon\) Regions and perform a TransactWriteItems operation in the US East \(Ohio\) Region, you may observe partially completed transactions in US West \(Oregon\) Region as changes are replicated\. Changes will only be replicated to other Regions once they have been committed in the source Region\.

**Note**  
Global tables “write around” DynamoDB Accelerator by updating DynamoDB directly\. As a result DAX will not be aware it is holding stale data\. The DAX cache will only be refreshed when the cache’s TTL expires\.
Tags on global tables do not automatically propagate\.

## Read and write throughput<a name="V2globaltables_HowItWorks.Throughput"></a>

Global tables manage read and write throughput in the following ways\.
+ The write capacity must be the same on all table instances across Regions\.
+ With Version 2019\.11\.21 \(Current\), if the table is set to support autoscaling or is in on\-demand mode then the write capacity is automatically kept in sync\. This means that a write capacity change to one table replicates to the others\. 
+ Read capacity can differ between Regions because reads may not be equal\. When adding a global replica to a table, the capacity of the source Region is propagated\. After creation you can adjust the read capacity for one replica, and this new setting is not transferred to the other side\.

## Consistency and conflict resolution<a name="V2globaltables_HowItWorks.conflict-resolution"></a>

Any changes made to any item in any replica table are replicated to all the other replicas within the same global table\. In a global table, a newly written item is usually propagated to all replica tables within a second\.

With a global table, each replica table stores the same set of data items\. DynamoDB does not support partial replication of only some of the items\.

An application can read and write data to any replica table\. If your application only uses eventually consistent reads and only issues reads against one AWS Region, it will work without any modification\. However, if your application requires strongly consistent reads, it must perform all of its strongly consistent reads and writes in the same Region\. DynamoDB does not support strongly consistent reads across Regions\. Therefore, if you write to one Region and read from another Region, the read response might include stale data that doesn't reflect the results of recently completed writes in the other Region\. 

If applications update the same item in different Regions at about the same time, conflicts can arise\. To help ensure eventual consistency, DynamoDB global tables use a *last writer wins* reconciliation between concurrent updates, in which DynamoDB makes a best effort to determine the last writer\. With this conflict resolution mechanism, all the replicas will agree on the latest update and converge toward a state in which they all have identical data\. 

## Availability and durability<a name="V2globaltables_HowItWorks.availability-durability"></a>

If a single AWS Region becomes isolated or degraded, your application can redirect to a different Region and perform reads and writes against a different replica table\. You can apply custom business logic to determine when to redirect requests to other Regions\. 

If a Region becomes isolated or degraded, DynamoDB keeps track of any writes that have been performed but have not yet been propagated to all of the replica tables\. When the Region comes back online, DynamoDB resumes propagating any pending writes from that Region to the replica tables in other Regions\. It also resumes propagating writes from other replica tables to the Region that is now back online\.