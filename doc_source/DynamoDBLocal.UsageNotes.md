# Usage Notes<a name="DynamoDBLocal.UsageNotes"></a>

Except for the endpoint, applications that run with the downloadable version of DynamoDB should also work with the DynamoDB web service\. However, when using DynamoDB locally, you should be aware of the following:
+ If you use the `-sharedDb` option, DynamoDB creates a single database file named *shared\-local\-instance\.db*\. Every program that connects to DynamoDB accesses this file\. If you delete the file, you lose any data you have stored in it\.
+ If you omit `-sharedDb`, the database file is named *myaccesskeyid\_region\.db*, with the AWS access key ID and region as they appear in your application configuration\. If you delete the file, you lose any data you have stored in it\.
+ If you use the `-inMemory` option, DynamoDB doesn't write any database files at all\. Instead, all data is written to memory, and the data is not saved when you terminate DynamoDB\.
+ If you use the `-optimizeDbBeforeStartup` option, you must also specify the `-dbPath` parameter so that DynamoDB can find its database file\.
+ The AWS SDKs for DynamoDB require that your application configuration specify an access key value and an AWS region value\. Unless you're using the `-sharedDb` or the `-inMemory` option, DynamoDB uses these values to name the local database file\.

  These values don't have to be valid AWS values to run locally\. However, you might find it convenient to use valid values so that you can run your code in the cloud later simply by changing the endpoint you're using\.

## Differences Between Downloadable DynamoDB and the DynamoDB Web Service<a name="DynamoDBLocal.Differences"></a>

The downloadable version of DynamoDB is intended for development and testing purposes only\. By comparison, the DynamoDB web service is a managed service with scalability, availability, and durability features that make it ideal for production use\. 

The downloadable version of DynamoDB differs from the web service in the following ways:
+ Regions and distinct AWS accounts are not supported at the client level\.
+ Provisioned throughput settings are ignored in downloadable DynamoDB, even though the `CreateTable` operation requires them\. For `CreateTable`, you can specify any numbers you want for provisioned read and write throughput, even though these numbers are not used\. You can call `UpdateTable` as many times as you want per day\. However, any changes to provisioned throughput values are ignored\.
+ `Scan` operations are performed sequentially\. Parallel scans are not supported\. The `Segment` and `TotalSegments` parameters of the `Scan` operation are ignored\.
+ The speed of read and write operations on table data is limited only by the speed of your computer\. `CreateTable`, `UpdateTable`, and `DeleteTable` operations occur immediately, and table state is always ACTIVE\. `UpdateTable` operations that change only the provisioned throughput settings on tables or global secondary indexes occur immediately\. If an `UpdateTable` operation creates or deletes any global secondary indexes, then those indexes transition through normal states \(such as CREATING and DELETING, respectively\) before they become an ACTIVE state\. The table remains ACTIVE during this time\.
+ Read operations are eventually consistent\. However, due to the speed of DynamoDB running on your computer, most reads appear to be strongly consistent\.
+ Consumed capacity units are not tracked\. In operation responses, nulls are returned instead of capacity units\.
+ Item collection metrics and item collection sizes are not tracked\. In operation responses, nulls are returned instead of item collection metrics\.
+ In DynamoDB, there is a 1 MB limit on data returned per result set\. Both the DynamoDB web service and the downloadable version enforce this limit\. However, when querying an index, the DynamoDB service calculates only the size of the projected key and attributes\. By contrast, the downloadable version of DynamoDB calculates the size of the entire item\.
+ If you're using DynamoDB Streams, the rate at which shards are created might differ\. In the DynamoDB web service, shard creation behavior is partially influenced by table partition activity\. When you run DynamoDB locally, there is no table partitioning\. In either case, shards are ephemeral, so your application should not be dependent on shard behavior\.