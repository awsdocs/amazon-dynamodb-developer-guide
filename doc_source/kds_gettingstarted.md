# Getting started with Kinesis Data Streams for Amazon DynamoDB<a name="kds_gettingstarted"></a>

   Get started quickly to learn and implement change data capture with DynamoDB and Kinesis Data Streams to integrate with other AWS services\.   

This section describes how to use Kinesis Data Streams for Amazon DynamoDB tables with the Amazon DynamoDB console, the AWS Command Line Interface \(AWS CLI\), and the API\.

All of these examples use the `Music` DynamoDB table that was created as part of the [Getting started with DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GettingStartedDynamoDB.html) tutorial\.

To learn more about how to build consumers and connect your Kinesis data stream to other AWS services, see [Reading data from Kinesis Data Streams](https://docs.aws.amazon.com/streams/latest/dev/building-consumers.html) in the *Amazon Kinesis Data Streams developer guide*\.

**Note**  
 When you're first using KDS shards, we recommend setting your shards to scale up and down with usage patterns\. After you have accumulated more data on usage patterns, you can adjust the shards in your stream to match\. 

------
#### [ Console ]

1. Sign in to the AWS Management Console and open the Kinesis console at https://console\.aws\.amazon\.com/kinesis/\.

1. Choose **Create data stream** and follow the instructions to create a stream called `samplestream`\. 

1. Open the DynamoDB console at https://console\.aws\.amazon\.com/dynamodb/\.

1. In the navigation pane on the left side of the console, choose **Tables**\.

1. Choose the **Music** table\.

1. Choose the **Exports and streams** tab\.

1. Under **Kinesis data stream details**, choose the **Enable** button\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/KDSEnable.png)

1. Choose **samplestream** from the dropdown list\.

1. Choose the **Enable stream** button\.

------
#### [ AWS CLI ]

1. Create a Kinesis Data Streams named `samplestream` by using the [create\-stream command](https://docs.aws.amazon.com/cli/latest/reference/kinesis/create-stream.html)\.

   ```
   aws kinesis create-stream --stream-name samplestream --shard-count 3 
   ```

   See [Shard management considerations for Kinesis Data Streams](kds_using-shards-and-metrics.md#kds_using-shards-and-metrics.shardmanagment) before setting the number of shards for the Kinesis data stream\.

1. Check that the Kinesis stream is active and ready for use by using the [describe\-stream command](https://docs.aws.amazon.com/cli/latest/reference/kinesis/describe-stream.html)\.

   ```
   aws kinesis describe-stream --stream-name samplestream
   ```

1. Enable Kinesis streaming on the DynamoDB table by using the DynamoDB `enable-kinesis-streaming-destination` command\. Replace the `stream-arn` value with the one that was returned by `describe-stream` in the previous step\.

   ```
   aws dynamodb enable-kinesis-streaming-destination \
       --table-name Music \
       --stream-arn arn:aws:kinesis:us-west-2:123456789012:stream/samplestream
   ```

1. Check if Kinesis streaming is active on the table by using the DynamoDB `describe-kinesis-streaming-destination` command\.

   ```
   aws dynamodb describe-kinesis-streaming-destination --table-name Music
   ```

1. Write data to the DynamoDB table by using the `put-item` command, as described in the [DynamoDB Developer Guide](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/getting-started-step-2.html)\.

   ```
   aws dynamodb put-item \
       --table-name Music  \
       --item \
           '{"Artist": {"S": "No One You Know"}, "SongTitle": {"S": "Call Me Today"}, "AlbumTitle": {"S": "Somewhat Famous"}, "Awards": {"N": "1"}}'
   
   aws dynamodb put-item \
       --table-name Music \
       --item \
           '{"Artist": {"S": "Acme Band"}, "SongTitle": {"S": "Happy Day"}, "AlbumTitle": {"S": "Songs About Life"}, "Awards": {"N": "10"} }'
   ```

1. Use the Kinesis [ get\-records](https://docs.aws.amazon.com/cli/latest/reference/kinesis/get-records.html) CLI command to retrieve the Kinesis stream contents\. Then use the following code snippet to deserialize the stream content\.

   ```
   /**
    * Takes as input a Record fetched from Kinesis and does arbitrary processing as an example.
    */
   public void processRecord(Record kinesisRecord) throws IOException {
       ByteBuffer kdsRecordByteBuffer = kinesisRecord.getData();
       JsonNode rootNode = OBJECT_MAPPER.readTree(kdsRecordByteBuffer.array());
       JsonNode dynamoDBRecord = rootNode.get("dynamodb");
       JsonNode oldItemImage = dynamoDBRecord.get("OldImage");
       JsonNode newItemImage = dynamoDBRecord.get("NewImage");
       Instant recordTimestamp = fetchTimestamp(dynamoDBRecord);
   
       /**
        * Say for example our record contains a String attribute named "stringName" and we want to fetch the value
        * of this attribute from the new item image. The following code fetches this value.
        */
       JsonNode attributeNode = newItemImage.get("stringName");
       JsonNode attributeValueNode = attributeNode.get("S"); // Using DynamoDB "S" type attribute
       String attributeValue = attributeValueNode.textValue();
       System.out.println(attributeValue);
   }
   
   private Instant fetchTimestamp(JsonNode dynamoDBRecord) {
       JsonNode timestampJson = dynamoDBRecord.get("ApproximateCreationDateTime");
       return Instant.ofEpochMilli(timestampJson.longValue());
   }
   ```

------
#### [ Java ]

1. Follow the instructions in the Kinesis Data Streams developer guide to [create](https://docs.aws.amazon.com/streams/latest/dev/kinesis-using-sdk-java-create-stream.html) a Kinesis data stream named `samplestream` using Java\.

   See [Shard management considerations for Kinesis Data Streams](kds_using-shards-and-metrics.md#kds_using-shards-and-metrics.shardmanagment) before setting the number of shards for the Kinesis data stream\. 

1. Use the following code snippet to enable Kinesis streaming on the DynamoDB table

   ```
   EnableKinesisStreamingDestinationRequest enableKdsRequest = EnableKinesisStreamingDestinationRequest.builder()
       .tableName(tableName)
       .streamArn(kdsArn)
       .build();
   
   EnableKinesisStreamingDestinationResponse enableKdsResponse = ddbClient.enableKinesisStreamingDestination(enableKdsRequest);
   ```

1. Follow the instructions in the *Kinesis Data Streams developer guide* to [read](https://docs.aws.amazon.com/streams/latest/dev/building-consumers.html) from the created data stream\.

1. Use the following code snippet to deserialize the stream content

   ```
   /**
    * Takes as input a Record fetched from Kinesis and does arbitrary processing as an example.
    */
   public void processRecord(Record kinesisRecord) throws IOException {
       ByteBuffer kdsRecordByteBuffer = kinesisRecord.getData();
       JsonNode rootNode = OBJECT_MAPPER.readTree(kdsRecordByteBuffer.array());
       JsonNode dynamoDBRecord = rootNode.get("dynamodb");
       JsonNode oldItemImage = dynamoDBRecord.get("OldImage");
       JsonNode newItemImage = dynamoDBRecord.get("NewImage");
       Instant recordTimestamp = fetchTimestamp(dynamoDBRecord);
   
       /**
        * Say for example our record contains a String attribute named "stringName" and we wanted to fetch the value
        * of this attribute from the new item image, the below code would fetch this.
        */
       JsonNode attributeNode = newItemImage.get("stringName");
       JsonNode attributeValueNode = attributeNode.get("S"); // Using DynamoDB "S" type attribute
       String attributeValue = attributeValueNode.textValue();
       System.out.println(attributeValue);
   }
   
   private Instant fetchTimestamp(JsonNode dynamoDBRecord) {
       JsonNode timestampJson = dynamoDBRecord.get("ApproximateCreationDateTime");
       return Instant.ofEpochMilli(timestampJson.longValue());
   }
   ```

------