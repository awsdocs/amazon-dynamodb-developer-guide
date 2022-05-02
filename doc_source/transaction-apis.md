# Amazon DynamoDB Transactions: How It Works<a name="transaction-apis"></a>

With Amazon DynamoDB transactions, you can group multiple actions together and submit them as a single all\-or\-nothing `TransactWriteItems` or `TransactGetItems` operation\. The following sections describe API operations, capacity management, best practices, and other details about using transactional operations in DynamoDB\.

**Topics**
+ [TransactWriteItems API](#transaction-apis-txwriteitems)
+ [TransactGetItems API](#transaction-apis-txgetitems)
+ [Isolation Levels for DynamoDB Transactions](#transaction-isolation)
+ [Transaction Conflict Handling in DynamoDB](#transaction-conflict-handling)
+ [Using Transactional APIs in DynamoDB Accelerator \(DAX\)](#transaction-apis-dax)
+ [Capacity Management for Transactions](#transaction-capacity-handling)
+ [Best Practices for Transactions](#transaction-best-practices)
+ [Using Transactional APIs with Global Tables](#transaction-integration)
+ [DynamoDB Transactions vs\. the AWSLabs Transactions Client Library](#transaction-vs-library)

## TransactWriteItems API<a name="transaction-apis-txwriteitems"></a>

`TransactWriteItems` is a synchronous and idempotent write operation that groups up to 25 write actions in a single all\-or\-nothing operation\. These actions can target up to 25 distinct items in one or more DynamoDB tables within the same AWS account and in the same Region\. The aggregate size of the items in the transaction cannot exceed 4 MB\. The actions are completed atomically so that either all of them succeed or none of them succeeds\.

**Note**  
A `TransactWriteItems` operation differs from a `BatchWriteItem` operation in that all the actions it contains must be completed successfully, or no changes are made at all\. With a `BatchWriteItem` operation, it is possible that only some of the actions in the batch succeed while the others do not\.

You can't target the same item with multiple operations within the same transaction\. For example, you can't perform a `ConditionCheck` and also an `Update` action on the same item in the same transaction\.

You can add the following types of actions to a transaction:
+ `Put` — Initiates a `PutItem` operation to create a new item or replace an old item with a new item, conditionally or without specifying any condition\.
+ `Update` — Initiates an `UpdateItem` operation to edit an existing item's attributes or add a new item to the table if it does not already exist\. Use this action to add, delete, or update attributes on an existing item conditionally or without a condition\.
+ `Delete` — Initiates a `DeleteItem` operation to delete a single item in a table identified by its primary key\.
+ `ConditionCheck` — Checks that an item exists or checks the condition of specific attributes of the item\.

Once a transaction completes, the changes made within that transaction are propagated to global secondary indexes \(GSIs\), streams, and backups\. Since propagation is not immediate or instantaneous, if a table is restored from backup \([RestoreTableFromBackup](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_RestoreTableFromBackup.html)\) or exported to a point in time \([ExportTableToPointInTime](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_ExportTableToPointInTime.html)\) mid\-propagation, it might contain some but not all of the changes made during a recent transaction\.

### Idempotency<a name="transaction-apis-txwriteitems-idempotency"></a>

You can optionally include a client token when you make a `TransactWriteItems` call to ensure that the request is *idempotent*\. Making your transactions idempotent helps prevent application errors if the same operation is submitted multiple times due to a connection time\-out or other connectivity issue\.

If the original `TransactWriteItems` call was successful, the subsequent `TransactWriteItems` calls with the same client token return successfully without making any changes\. If the `ReturnConsumedCapacity` parameter is set, the initial `TransactWriteItems` call returns the number of write capacity units consumed in making the changes\. Subsequent `TransactWriteItems` calls with the same client token return the number of read capacity units consumed in reading the item\.

**Important Points about Idempotency**
+ A client token is valid for 10 minutes after the request that uses it finishes\. After 10 minutes, any request that uses the same client token is treated as a new request\. You should not reuse the same client token for the same request after 10 minutes\.
+ If you repeat a request with the same client token within the 10\-minute idempotency window but change some other request parameter, DynamoDB returns an `IdempotentParameterMismatch` exception\.

### Error Handling for Writing<a name="transaction-apis-txwriteitems-errors"></a>

Write transactions don't succeed under the following circumstances:
+ When a condition in one of the condition expressions is not met\.
+ When a transaction validation error occurs because more than one action in the same `TransactWriteItems` operation targets the same item\.
+ When a `TransactWriteItems` request conflicts with an ongoing `TransactWriteItems` operation on one or more items in the `TransactWriteItems` request\. In this case, the request fails with a `TransactionCanceledException`\.
+ When there is insufficient provisioned capacity for the transaction to be completed\.
+ When an item size becomes too large \(larger than 400 KB\), or a local secondary index \(LSI\) becomes too large, or a similar validation error occurs because of changes made by the transaction\.
+ When there is a user error, such as an invalid data format\.

 For more information about how conflicts with `TransactWriteItems` operations are handled, see [Transaction Conflict Handling in DynamoDB](#transaction-conflict-handling)\.

## TransactGetItems API<a name="transaction-apis-txgetitems"></a>

`TransactGetItems` is a synchronous read operation that groups up to 25 `Get` actions together\. These actions can target up to 25 distinct items in one or more DynamoDB tables within the same AWS account and Region\. The aggregate size of the items in the transaction can't exceed 4 MB\. 

The `Get` actions are performed atomically so that either all of them succeed or all of them fail:
+ `Get` — Initiates a `GetItem` operation to retrieve a set of attributes for the item with the given primary key\. If no matching item is found, `Get` does not return any data\.

### Error Handling for Reading<a name="transaction-apis-txgetitems-errors"></a>

Read transactions don't succeed under the following circumstances:
+ When a `TransactGetItems` request conflicts with an ongoing `TransactWriteItems` operation on one or more items in the `TransactGetItems` request\. In this case, the request fails with a `TransactionCanceledException`\.
+ When there is insufficient provisioned capacity for the transaction to be completed\.
+ When there is a user error, such as an invalid data format\.

 For more information about how conflicts with `TransactGetItems` operations are handled, see [Transaction Conflict Handling in DynamoDB](#transaction-conflict-handling)\.

## Isolation Levels for DynamoDB Transactions<a name="transaction-isolation"></a>

The isolation levels of transactional operations \(`TransactWriteItems` or `TransactGetItems`\) and other operations are as follows\.

### SERIALIZABLE<a name="transaction-isolation-serializable"></a>

*Serializable* isolation ensures that the results of multiple concurrent operations are the same as if no operation begins until the previous one has finished\.

There is serializable isolation between the following types of operation:
+ Between any transactional operation and any standard write operation \(`PutItem`, `UpdateItem`, or `DeleteItem`\)\.
+ Between any transactional operation and any standard read operation \(`GetItem`\)\.
+ Between a `TransactWriteItems` operation and a `TransactGetItems` operation\.

Although there is serializable isolation between transactional operations, and each individual standard writes in a `BatchWriteItem` operation, there is no serializable isolation between the transaction and the `BatchWriteItem` operation as a unit\.

Similarly, the isolation level between a transactional operation and individual `GetItems` in a `BatchGetItem` operation is serializable\. But the isolation level between the transaction and the `BatchGetItem` operation as a unit is *read\-committed*\.

A single `GetItem` request is serializable with respect to a `TransactWriteItems` request in one of two ways, either before or after the `TransactWriteItems` request\. Multiple `GetItem` requests, against keys in a concurrent `TransactWriteItems` requests can be run in any order, and therefore the results are *read\-committed*\.

For example, if `GetItem` requests for item A and item B are run concurrently with a `TransactWriteItems` request that modifies both item A and item B, there are four possibilities:
+ Both `GetItem` requests are run before the `TransactWriteItems` request\.
+ Both `GetItem` requests are run after the `TransactWriteItems` request\.
+ `GetItem` request for item A is run before the `TransactWriteItems` request\. For item B the `GetItem` is run after `TransactWriteItems`\.
+ `GetItem` request for item B is run before the `TransactWriteItems` request\. For item A the `GetItem` is run after `TransactWriteItems`\.

If serializable isolation level is preferred for multiple `GetItem` requests, then please use `TransactGetItems`\.

### READ\-COMMITTED<a name="transaction-isolation-read-committed"></a>

*Read\-committed* isolation ensures that read operations always return committed values for an item\. Read\-committed isolation does not prevent modifications of the item immediately after the read operation\.

The isolation level is read\-committed between any transactional operation and any read operation that involves multiple standard reads \(`BatchGetItem`, `Query`, or `Scan`\)\. If a transactional write updates an item in the middle of a `BatchGetItem`, `Query`, or `Scan` operation, the read operation returns the new committed value\.

### Operation Summary<a name="transaction-isolation-table"></a>

To summarize, the following table shows the isolation levels between a transaction operation \(`TransactWriteItems` or `TransactGetItems`\) and other operations\.


| Operation | Isolation Level | 
| --- | --- | 
| `DeleteItem` | *Serializable* | 
| `PutItem` | *Serializable* | 
| `UpdateItem` | *Serializable* | 
| `GetItem` | *Serializable* | 
| `BatchGetItem` | *Read\-committed*\* | 
| `BatchWriteItem` | *NOT Serializable*\* | 
| `Query` | *Read\-committed* | 
| `Scan` | *Read\-committed* | 
| Other transactional operation | *Serializable* | 

Levels marked with an asterisk \(\*\) apply to the operation as a unit\. However, individual actions within those operations have a *serializable* isolation level\.

## Transaction Conflict Handling in DynamoDB<a name="transaction-conflict-handling"></a>

A transactional conflict can occur during concurrent item\-level requests on an item within a transaction\. Transaction conflicts can occur in the following scenarios: 
+ A `PutItem`, `UpdateItem`, or `DeleteItem` request for an item conflicts with an ongoing `TransactWriteItems` request that includes the same item\.
+ An item within a `TransactWriteItems` request is part of another ongoing `TransactWriteItems` request\.
+ An item within a `TransactGetItems` request is part of an ongoing `TransactWriteItems`, `BatchWriteItem`, `PutItem`, `UpdateItem`, or `DeleteItem` request\.

**Note**  
When a `PutItem`, `UpdateItem`, or `DeleteItem` request is rejected, the request fails with a `TransactionConflictException`\. 
If any item\-level request within `TransactWriteItems` or `TransactGetItems` is rejected, the request fails with a `TransactionCanceledException`\. If that request fails, AWS SDKs do not retry the request\.  
If you are using the AWS SDK for Java, the exception contains the list of [CancellationReasons](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_CancellationReason.html), ordered according to the list of items in the `TransactItems` request parameter\. For other languages, a string representation of the list is included in the exception’s error message\. 
If an ongoing `TransactWriteItems` or `TransactGetItems` operation conflicts with a concurrent `GetItem` request, both operations can succeed\.

The [TransactionConflict CloudWatch metric](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/metrics-dimensions.html) is incremented for each failed item\-level request\.

## Using Transactional APIs in DynamoDB Accelerator \(DAX\)<a name="transaction-apis-dax"></a>

`TransactWriteItems` and `TransactGetItems` are both supported in DynamoDB Accelerator \(DAX\) with the same isolation levels as in DynamoDB\.

`TransactWriteItems` writes through DAX\. DAX passes a `TransactWriteItems` call to DynamoDB and returns the response\. To populate the cache after the write, DAX calls `TransactGetItems` in the background for each item in the `TransactWriteItems` operation, which consumes additional read capacity units\. \(For more information, see [Capacity Management for Transactions](#transaction-capacity-handling)\.\) This functionality enables you to keep your application logic simple and use DAX for both transactional operations and nontransactional ones\.

`TransactGetItems` calls are passed through DAX without the items being cached locally\. This is the same behavior as for strongly consistent read APIs in DAX\.

## Capacity Management for Transactions<a name="transaction-capacity-handling"></a>

There is no additional cost to enable transactions for your DynamoDB tables\. You pay only for the reads or writes that are part of your transaction\. DynamoDB performs two underlying reads or writes of every item in the transaction: one to prepare the transaction and one to commit the transaction\. The two underlying read/write operations are visible in your Amazon CloudWatch metrics\.

Plan for the additional reads and writes that are required by transactional APIs when you are provisioning capacity to your tables\. For example, suppose that your application runs one transaction per second, and each transaction writes three 500\-byte items in your table\. Each item requires two write capacity units \(WCUs\): one to prepare the transaction and one to commit the transaction\. Therefore, you would need to provision six WCUs to the table\. 

If you were using DynamoDB Accelerator \(DAX\) in the previous example, you would also use two read capacity units \(RCUs\) for each item in the `TransactWriteItems` call\. So you would need to provision six additional RCUs to the table\.

Similarly, if your application runs one read transaction per second, and each transaction reads three 500\-byte items in your table, you would need to provision six read capacity units \(RCUs\) to the table\. Reading each item requires two RCUs: one to prepare the transaction and one to commit the transaction\.

Also, default SDK behavior is to retry transactions in case of a `TransactionInProgressException` exception\. Plan for the additional read\-capacity units \(RCUs\) that these retries consume\. The same is true if you are retrying transactions in your own code using a `ClientRequestToken`\.

## Best Practices for Transactions<a name="transaction-best-practices"></a>

Consider the following recommended practices when using DynamoDB transactions\.
+ Enable automatic scaling on your tables, or ensure that you have provisioned enough throughput capacity to perform the two read or write operations for every item in your transaction\.
+ If you are not using an AWS provided SDK, include a `ClientRequestToken` attribute when you make a `TransactWriteItems` call to ensure that the request is idempotent\.
+ Don't group operations together in a transaction if it's not necessary\. For example, if a single transaction with 10 operations can be broken up into multiple transactions without compromising the application correctness, we recommend splitting up the transaction\. Simpler transactions improve throughput and are more likely to succeed\. 
+ Multiple transactions updating the same items simultaneously can cause conflicts that cancel the transactions\. We recommend following DynamoDB best practices for data modeling to minimize such conflicts\.
+ If a set of attributes is often updated across multiple items as part of a single transaction, consider grouping the attributes into a single item to reduce the scope of the transaction\.
+ Avoid using transactions for ingesting data in bulk\. For bulk writes, it is better to use `BatchWriteItem`\.

## Using Transactional APIs with Global Tables<a name="transaction-integration"></a>

Transactional operations provide atomicity, consistency, isolation, and durability \(ACID\) guarantees only within the region where the write is made originally\. Transactions are not supported across regions in global tables\. For example, if you have a global table with replicas in the US East \(Ohio\) and US West \(Oregon\) regions and perform a TransactWriteItems operation in the US East \(N\. Virginia\) Region, you may observe partially completed transactions in US West \(Oregon\) Region as changes are replicated\. Changes will only be replicated to other regions once they have been committed in the source region\.

## DynamoDB Transactions vs\. the AWSLabs Transactions Client Library<a name="transaction-vs-library"></a>

DynamoDB transactions provide a more cost\-effective, robust, and performant replacement for the [AWSLabs](https://github.com/awslabs) transactions client library\. We suggest that you update your applications to use the native, server\-side transaction APIs\.