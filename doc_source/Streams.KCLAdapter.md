# Using the DynamoDB Streams Kinesis adapter to process stream records<a name="Streams.KCLAdapter"></a>

Using the Amazon Kinesis Adapter is the recommended way to consume streams from Amazon DynamoDB\. The DynamoDB Streams API is intentionally similar to that of Kinesis Data Streams, a service for real\-time processing of streaming data at massive scale\. In both services, data streams are composed of shards, which are containers for stream records\. Both services' APIs contain `ListStreams`, `DescribeStream`, `GetShards`, and `GetShardIterator` operations\. \(Although these DynamoDB Streams actions are similar to their counterparts in Kinesis Data Streams, they are not 100 percent identical\.\)

You can write applications for Kinesis Data Streams using the Kinesis Client Library \(KCL\)\. The KCL simplifies coding by providing useful abstractions above the low\-level Kinesis Data Streams API\. For more information about the KCL, see the [Developing consumers using the Kinesis client library](https://docs.aws.amazon.com/kinesis/latest/dev/developing-consumers-with-kcl.html) in the *Amazon Kinesis Data Streams Developer Guide*\.

As a DynamoDB Streams user, you can use the design patterns found within the KCL to process DynamoDB Streams shards and stream records\. To do this, you use the DynamoDB Streams Kinesis Adapter\. The Kinesis Adapter implements the Kinesis Data Streams interface so that the KCL can be used for consuming and processing records from DynamoDB Streams\. For instructions on how to set up and install the DynamoDB Streams Kinesis Adapter, see the [GitHub repository](https://github.com/awslabs/dynamodb-streams-kinesis-adapter) \.

The following diagram shows how these libraries interact with one another\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/streams-kinesis-adapter.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

With the DynamoDB Streams Kinesis Adapter in place, you can begin developing against the KCL interface, with the API calls seamlessly directed at the DynamoDB Streams endpoint\.

When your application starts, it calls the KCL to instantiate a worker\. You must provide the worker with configuration information for the application, such as the stream descriptor and AWS credentials, and the name of a record processor class that you provide\. As it runs the code in the record processor, the worker performs the following tasks:
+ Connects to the stream
+ Enumerates the shards within the stream
+ Coordinates shard associations with other workers \(if any\)
+ Instantiates a record processor for every shard it manages
+ Pulls records from the stream
+ Pushes the records to the corresponding record processor
+ Checkpoints processed records
+ Balances shard\-worker associations when the worker instance count changes
+ Balances shard\-worker associations when shards are split

**Note**  
For a description of the KCL concepts listed here, see [Developing consumers using the Kinesis client library](https://docs.aws.amazon.com/kinesis/latest/dev/developing-consumers-with-kcl.html) in the *Amazon Kinesis Data Streams Developer Guide*\.  
For more information on using streams with AWS Lambda see [DynamoDB Streams and AWS Lambda triggers](Streams.Lambda.md)