# DynamoDB local usage notes<a name="DynamoDBLocal.UsageNotes"></a>

Except for the endpoint, applications that run with the downloadable version of Amazon DynamoDB should also work with the DynamoDB web service\. However, when using DynamoDB locally, you should be aware of the following:
+ If you use the `-sharedDb` option, DynamoDB creates a single database file named *shared\-local\-instance\.db*\. Every program that connects to DynamoDB accesses this file\. If you delete the file, you lose any data that you have stored in it\.
+ If you omit `-sharedDb`, the database file is named *myaccesskeyid\_region\.db*, with the AWS access key ID and AWS Region as they appear in your application configuration\. If you delete the file, you lose any data that you have stored in it\.
+ If you use the `-inMemory` option, DynamoDB doesn't write any database files at all\. Instead, all data is written to memory, and the data is not saved when you terminate DynamoDB\.
+ If you use the `-optimizeDbBeforeStartup` option, you must also specify the `-dbPath` parameter so that DynamoDB can find its database file\.
+ The AWS SDKs for DynamoDB require that your application configuration specify an access key value and an AWS Region value\. Unless you're using the `-sharedDb` or the `-inMemory` option, DynamoDB uses these values to name the local database file\. These values don't have to be valid AWS values to run locally\. However, you might find it convenient to use valid values so that you can run your code in the cloud later by changing the endpoint you're using\.
+ DynamoDB local always returns null for `billingModeSummary.`

**Topics**
+ [Command line options](#DynamoDBLocal.CommandLineOptions)
+ [Setting the local endpoint](#DynamoDBLocal.Endpoint)
+ [Differences between downloadable DynamoDB and the DynamoDB web service](#DynamoDBLocal.Differences)

## Command line options<a name="DynamoDBLocal.CommandLineOptions"></a>

You can use the following command line options with the downloadable version of DynamoDB:
+ `-cors` `value` — Enables support for cross\-origin resource sharing \(CORS\) for JavaScript\. You must provide a comma\-separated "allow" list of specific domains\. The default setting for `-cors` is an asterisk \(\*\), which allows public access\.
+ `-dbPath` `value` — The directory where DynamoDB writes its database file\. If you don't specify this option, the file is written to the current directory\. You can't specify both `-dbPath` and `-inMemory` at once\.
+ `-delayTransientStatuses` — Causes DynamoDB to introduce delays for certain operations\. DynamoDB \(downloadable version\) can perform some tasks almost instantaneously, such as create/update/delete operations on tables and indexes\. However, the DynamoDB service requires more time for these tasks\. Setting this parameter helps DynamoDB running on your computer simulate the behavior of the DynamoDB web service more closely\. \(Currently, this parameter introduces delays only for global secondary indexes that are in either *CREATING* or *DELETING* status\.\)
+ `-help` — Prints a usage summary and options\.
+ `-inMemory` — DynamoDB runs in memory instead of using a database file\. When you stop DynamoDB, none of the data is saved\. You can't specify both `-dbPath` and `-inMemory` at once\.
+ `-optimizeDbBeforeStartup` — Optimizes the underlying database tables before starting DynamoDB on your computer\. You also must specify `-dbPath` when you use this parameter\.
+ `-port` `value` — The port number that DynamoDB uses to communicate with your application\. If you don't specify this option, the default port is `8000`\.
**Note**  
DynamoDB uses port 8000 by default\. If port 8000 is unavailable, this command throws an exception\. You can use the `-port` option to specify a different port number\. For a complete list of DynamoDB runtime options, including `-port` , type this command:  
`java -Djava.library.path=./DynamoDBLocal_lib -jar DynamoDBLocal.jar -help`
+ `-sharedDb` — If you specify `-sharedDb`, DynamoDB uses a single database file instead of separate files for each credential and Region\.

## Setting the local endpoint<a name="DynamoDBLocal.Endpoint"></a>

By default, the AWS SDKs and tools use endpoints for the Amazon DynamoDB web service\. To use the SDKs and tools with the downloadable version of DynamoDB, you must specify the local endpoint:

`http://localhost:8000`

### AWS Command Line Interface<a name="DynamoDBLocal.Endpoint.CLI"></a>

You can use the AWS Command Line Interface \(AWS CLI\) to interact with downloadable DynamoDB\. For example, you can use it to perform all the steps in [Creating tables and loading data for code examples in DynamoDB](SampleData.md)\.

To access DynamoDB running locally, use the `--endpoint-url` parameter\. The following is an example of using the AWS CLI to list the tables in DynamoDB on your computer\.

```
aws dynamodb list-tables --endpoint-url http://localhost:8000
```

**Note**  
The AWS CLI can't use the downloadable version of DynamoDB as a default endpoint\. Therefore, you must specify `--endpoint-url` with each AWS CLI command\.

### AWS SDKs<a name="DynamoDBLocal.Endpoint.SDK"></a>

The way you specify an endpoint depends on the programming language and AWS SDK you're using\. The following sections describe how to do this:
+ [Java: Setting the AWS Region and endpoint](CodeSamples.Java.md#CodeSamples.Java.RegionAndEndpoint) \(DynamoDB local supports the AWS SDK for Java V1 and V2\)
+ [\.NET: Setting the AWS Region and endpoint](CodeSamples.DotNet.md#CodeSamples.DotNet.RegionAndEndpoint)

**Note**  
For examples in other programming languages, see [Getting started with DynamoDB and the AWS SDKs](GettingStarted.md)\.

## Differences between downloadable DynamoDB and the DynamoDB web service<a name="DynamoDBLocal.Differences"></a>

The downloadable version of DynamoDB is intended for development and testing purposes only\. By comparison, the DynamoDB web service is a managed service with scalability, availability, and durability features that make it ideal for production use\. 

The downloadable version of DynamoDB differs from the web service in the following ways:
+  DynamoDB local is capped at 100 items for transactions\. 
+ AWS Regions and distinct AWS accounts are not supported at the client level\.
+ Provisioned throughput settings are ignored in downloadable DynamoDB, even though the `CreateTable` operation requires them\. For `CreateTable`, you can specify any numbers you want for provisioned read and write throughput, even though these numbers are not used\. You can call `UpdateTable` as many times as you want per day\. However, any changes to provisioned throughput values are ignored\.
+ `Scan` operations are performed sequentially\. Parallel scans are not supported\. The `Segment` and `TotalSegments` parameters of the `Scan` operation are ignored\.
+ The speed of read and write operations on table data is limited only by the speed of your computer\. `CreateTable`, `UpdateTable`, and `DeleteTable` operations occur immediately, and table state is always ACTIVE\. `UpdateTable` operations that change only the provisioned throughput settings on tables or global secondary indexes occur immediately\. If an `UpdateTable` operation creates or deletes any global secondary indexes, then those indexes transition through normal states \(such as CREATING and DELETING, respectively\) before they become an ACTIVE state\. The table remains ACTIVE during this time\.
+ Read operations are eventually consistent\. However, due to the speed of DynamoDB running on your computer, most reads appear to be strongly consistent\.
+ Item collection metrics and item collection sizes are not tracked\. In operation responses, nulls are returned instead of item collection metrics\.
+ In DynamoDB, there is a 1 MB limit on data returned per result set\. Both the DynamoDB web service and the downloadable version enforce this limit\. However, when querying an index, the DynamoDB service calculates only the size of the projected key and attributes\. By contrast, the downloadable version of DynamoDB calculates the size of the entire item\.
+ If you're using DynamoDB Streams, the rate at which shards are created might differ\. In the DynamoDB web service, shard\-creation behavior is partially influenced by table partition activity\. When you run DynamoDB locally, there is no table partitioning\. In either case, shards are ephemeral, so your application should not be dependent on shard behavior\.
+  `TransactionConflictExceptions` are not thrown by downloadable DynamoDB for transactional APIs\. We recommend that you use a Java mocking framework to simulate `TransactionConflictExceptions` in the DynamoDB handler to test how your application responds to conflicting transactions\. 
+ In the DynamoDB web service, table names are case sensitive\. A table named `Authors` and one named `authors` can both exist as separate tables\. In the downloadable version, table names are case insensitive, and attempting to create these two tables would result in an error\.