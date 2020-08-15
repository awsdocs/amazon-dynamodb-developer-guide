# Capturing Table Activity with DynamoDB Streams<a name="Streams"></a>

Many applications can benefit from the ability to capture changes to items stored in a DynamoDB table, at the point in time when such changes occur\. The following are some example use cases:
+ An application in one AWS Region modifies the data in a DynamoDB table\. A second application in another Region reads these data modifications and writes the data to another table, creating a replica that stays in sync with the original table\.
+ A popular mobile app modifies data in a DynamoDB table, at the rate of thousands of updates per second\. Another application captures and stores data about these updates, providing near\-real\-time usage metrics for the mobile app\.
+ A global multi\-player game has a multi\-master topology, storing data in multiple AWS Regions\. Each master stays in sync by consuming and replaying the changes that occur in the remote Regions\.
+ An application automatically sends notifications to the mobile devices of all friends in a group as soon as one friend uploads a new picture\.
+ A new customer adds data to a DynamoDB table\. This event invokes another application that sends a welcome email to the new customer\.

DynamoDB Streams enables solutions such as these, and many others\. DynamoDB Streams captures a time\-ordered sequence of item\-level modifications in any DynamoDB table and stores this information in a log for up to 24 hours\. Applications can access this log and view the data items as they appeared before and after they were modified, in near\-real time\.

 Encryption at rest encrypts the data in DynamoDB streams\. For more information, see [DynamoDB Encryption at Rest](EncryptionAtRest.md)\.

A *DynamoDB stream* is an ordered flow of information about changes to items in a DynamoDB table\. When you enable a stream on a table, DynamoDB captures information about every modification to data items in the table\.

Whenever an application creates, updates, or deletes items in the table, DynamoDB Streams writes a stream record with the primary key attributes of the items that were modified\. A *stream record* contains information about a data modification to a single item in a DynamoDB table\. You can configure the stream so that the stream records capture additional information, such as the "before" and "after" images of modified items\.

DynamoDB Streams helps ensure the following:
+ Each stream record appears exactly once in the stream\.
+ For each item that is modified in a DynamoDB table, the stream records appear in the same sequence as the actual modifications to the item\.

DynamoDB Streams writes stream records in near\-real time so that you can build applications that consume these streams and take action based on the contents\.

**Topics**
+ [Endpoints for DynamoDB Streams](#Streams.Endpoints)
+ [Enabling a Stream](#Streams.Enabling)
+ [Reading and Processing a Stream](#Streams.Processing)
+ [DynamoDB Streams and Time to Live](time-to-live-ttl-streams.md)
+ [Using the DynamoDB Streams Kinesis Adapter to Process Stream Records](Streams.KCLAdapter.md)
+ [DynamoDB Streams Low\-Level API: Java Example](Streams.LowLevel.Walkthrough.md)
+ [Cross\-Region Replication](Streams.CrossRegionRepl.md)
+ [DynamoDB Streams and AWS Lambda Triggers](Streams.Lambda.md)

## Endpoints for DynamoDB Streams<a name="Streams.Endpoints"></a>

AWS maintains separate endpoints for DynamoDB and DynamoDB Streams\. To work with database tables and indexes, your application must access a DynamoDB endpoint\. To read and process DynamoDB Streams records, your application must access a DynamoDB Streams endpoint in the same Region\.

The naming convention for DynamoDB Streams endpoints is `streams.dynamodb.<region>.amazonaws.com`\. For example, if you use the endpoint `dynamodb.us-west-2.amazonaws.com` to access DynamoDB, you would use the endpoint `streams.dynamodb.us-west-2.amazonaws.com` to access DynamoDB Streams\.

**Note**  
For a complete list of DynamoDB and DynamoDB Streams Regions and endpoints, see [Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html) in the *AWS General Reference*\.

The AWS SDKs provide separate clients for DynamoDB and DynamoDB Streams\. Depending on your requirements, your application can access a DynamoDB endpoint, a DynamoDB Streams endpoint, or both at the same time\. To connect to both endpoints, your application must instantiate two clients—one for DynamoDB and one for DynamoDB Streams\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/streams-endpoints.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

## Enabling a Stream<a name="Streams.Enabling"></a>

You can enable a stream on a new table when you create it\. You can also enable or disable a stream on an existing table, or change the settings of a stream\. DynamoDB Streams operates asynchronously, so there is no performance impact on a table if you enable a stream\.

The easiest way to manage DynamoDB Streams is by using the AWS Management Console\.

1. Sign in to the AWS Management Console and open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. On the DynamoDB console dashboard, choose **Tables**\.

1. On the **Overview** tab, choose **Manage Stream**\.

1. In the **Manage Stream** window, choose the information that will be written to the stream whenever the data in the table is modified:
   + **Keys only** — Only the key attributes of the modified item\.
   + **New image** — The entire item, as it appears after it was modified\.
   + **Old image** — The entire item, as it appeared before it was modified\.
   + **New and old images** — Both the new and the old images of the item\.

   When the settings are as you want them, choose **Enable**\.

1. \(Optional\) To disable an existing stream, choose **Manage Stream** and then choose **Disable**\.

You can also use the `CreateTable` or `UpdateTable` API operations to enable or modify a stream\. The `StreamSpecification` parameter determines how the stream is configured:
+ `StreamEnabled` — Specifies whether a stream is enabled \(`true`\) or disabled \(`false`\) for the table\.
+ `StreamViewType` — Specifies the information that will be written to the stream whenever data in the table is modified:
  + `KEYS_ONLY` — Only the key attributes of the modified item\.
  + `NEW_IMAGE` — The entire item, as it appears after it was modified\.
  + `OLD_IMAGE` — The entire item, as it appeared before it was modified\.
  + `NEW_AND_OLD_IMAGES` — Both the new and the old images of the item\.

You can enable or disable a stream at any time\. However, you receive a `ResourceInUseException` if you try to enable a stream on a table that already has a stream\. You receive a `ValidationException` if you try to disable a stream on a table that doesn't have a stream\.

When you set `StreamEnabled` to `true`, DynamoDB creates a new stream with a unique stream descriptor assigned to it\. If you disable and then re\-enable a stream on the table, a new stream is created with a different stream descriptor\.

Every stream is uniquely identified by an Amazon Resource Name \(ARN\)\. The following is an example ARN for a stream on a DynamoDB table named `TestTable`\.

```
arn:aws:dynamodb:us-west-2:111122223333:table/TestTable/stream/2015-05-11T21:21:33.291
```

To determine the latest stream descriptor for a table, issue a DynamoDB `DescribeTable` request and look for the `LatestStreamArn` element in the response\.

## Reading and Processing a Stream<a name="Streams.Processing"></a>

To read and process a stream, your application must connect to a DynamoDB Streams endpoint and issue API requests\.

A stream consists of *stream records*\. Each stream record represents a single data modification in the DynamoDB table to which the stream belongs\. Each stream record is assigned a sequence number, reflecting the order in which the record was published to the stream\.

Stream records are organized into groups, or *shards*\. Each shard acts as a container for multiple stream records, and contains information required for accessing and iterating through these records\. The stream records within a shard are removed automatically after 24 hours\.

Shards are ephemeral: They are created and deleted automatically, as needed\. Any shard can also split into multiple new shards; this also occurs automatically\. \(It's also possible for a parent shard to have just one child shard\.\) A shard might split in response to high levels of write activity on its parent table, so that applications can process records from multiple shards in parallel\.

If you disable a stream, any shards that are open will be closed\. The data in the stream will continue to be readable for 24 hours\.

Because shards have a lineage \(parent and children\), an application must always process a parent shard before it processes a child shard\. This helps ensure that the stream records are also processed in the correct order\. \(If you use the DynamoDB Streams Kinesis Adapter, this is handled for you\. Your application processes the shards and stream records in the correct order\. It automatically handles new or expired shards, in addition to shards that split while the application is running\. For more information, see [Using the DynamoDB Streams Kinesis Adapter to Process Stream Records](Streams.KCLAdapter.md)\.\)

The following diagram shows the relationship between a stream, shards in the stream, and stream records in the shards\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/streams-terminology.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

**Note**  
If you perform a `PutItem` or `UpdateItem` operation that does not change any data in an item, DynamoDB Streams does *not* write a stream record for that operation\.

To access a stream and process the stream records within, you must do the following:
+ Determine the unique ARN of the stream that you want to access\.
+ Determine which shards in the stream contain the stream records that you are interested in\.
+ Access the shards and retrieve the stream records that you want\.

**Note**  
No more than two processes at most should be reading from the same streams shard at the same time\. Having more than two readers per shard can result in throttling\.

The DynamoDB Streams API provides the following actions for use by application programs:
+  `[ListStreams](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_streams_ListStreams.html)` — Returns a list of stream descriptors for the current account and endpoint\. You can optionally request just the stream descriptors for a particular table name\.
+ `[DescribeStream](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_streams_DescribeStream.html)` — Returns detailed information about a given stream\. The output includes a list of shards associated with the stream, including the shard IDs\.
+ `[GetShardIterator](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_streams_GetShardIterator.html)` — Returns a *shard iterator*, which describes a location within a shard\. You can request that the iterator provide access to the oldest point, the newest point, or a particular point in the stream\.
+ `[GetRecords](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_streams_GetRecords.html)` — Returns the stream records from within a given shard\. You must provide the shard iterator returned from a `GetShardIterator` request\.

For complete descriptions of these API operations, including example requests and responses, see the [Amazon DynamoDB Streams API Reference](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Operations_Amazon_DynamoDB_Streams.html)\.

### Data Retention Limit for DynamoDB Streams<a name="Streams.DataRetention"></a>

All data in DynamoDB Streams is subject to a 24\-hour lifetime\. You can retrieve and analyze the last 24 hours of activity for any given table\. However, data that is older than 24 hours is susceptible to trimming \(removal\) at any moment\.

If you disable a stream on a table, the data in the stream continues to be readable for 24 hours\. After this time, the data expires and the stream records are automatically deleted\. There is no mechanism for manually deleting an existing stream\. You must wait until the retention limit expires \(24 hours\), and all the stream records will be deleted\.