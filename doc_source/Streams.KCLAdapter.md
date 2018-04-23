# Using the DynamoDB Streams Kinesis Adapter to Process Stream Records<a name="Streams.KCLAdapter"></a>

Using the Kinesis Adapter is the recommended way to consume Streams from DynamoDB\.

The DynamoDB Streams API is intentionally similar to that of Kinesis Streams, a service for real\-time processing of streaming data at massive scale\. In both services, data streams are composed of shards, which are containers for stream records\. Both services' APIs contain `ListStreams`, `DescribeStream`, `GetShards`, and `GetShardIterator` actions\. \(Even though these DynamoDB Streams actions are similar to their counterparts in Kinesis Streams, they are not 100% identical\.\)

You can write applications for Kinesis Streams using the Kinesis Client Library \(KCL\)\. The KCL simplifies coding by providing useful abstractions above the low\-level Kinesis Streams API\. For more information on the KCL, go to the [Amazon Kinesis Developer Guide](http://docs.aws.amazon.com/kinesis/latest/dev/developing-consumers-with-kcl.html)\.

As a DynamoDB Streams user, you can leverage the design patterns found within the KCL to process DynamoDB Streams shards and stream records\. To do this, you use the DynamoDB Streams Kinesis Adapter\. The Kinesis Adapter implements the Kinesis Streams interface, so that the KCL can be used for consuming and processing records from DynamoDB Streams\.

The following diagram shows how these libraries interact with one another\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/streams-kinesis-adapter.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

With the DynamoDB Streams Kinesis Adapter in place, you can begin developing against the KCL interface, with the API calls seamlessly directed at the DynamoDB Streams endpoint\.

When your application starts, it calls the KCL to instantiate a worker\. You must provide the worker with configuration information for the application, such as the stream descriptor and AWS credentials, and the name of a record processor class that you provide\. As it runs the code in the record processor, the worker performs the following tasks:
+ Connects to the stream\.
+ Enumerates the shards within the stream\.
+ Coordinates shard associations with other workers \(if any\)\.
+ Instantiates a record processor for every shard it manages\.
+ Pulls records from the stream\.
+ Pushes the records to the corresponding record processor\.
+ Checkpoints processed records\.
+ Balances shard\-worker associations when the worker instance count changes\.
+ Balances shard\-worker associations when shards are split\.

**Note**  
For a description of the KCL concepts listed above, go to [Developing Amazon Kinesis Consumers Using the Amazon Kinesis Client Library](http://docs.aws.amazon.com/kinesis/latest/dev/developing-consumers-with-kcl.html) in the *Amazon Kinesis Developer Guide*\.