# DescribeStream<a name="API_streams_DescribeStream"></a>

Returns information about a stream, including the current status of the stream, its Amazon Resource Name \(ARN\), the composition of its shards, and its corresponding DynamoDB table\.

**Note**  
You can call `DescribeStream` at a maximum rate of 10 times per second\.

Each shard in the stream has a `SequenceNumberRange` associated with it\. If the `SequenceNumberRange` has a `StartingSequenceNumber` but no `EndingSequenceNumber`, then the shard is still open \(able to receive more stream records\)\. If both `StartingSequenceNumber` and `EndingSequenceNumber` are present, then that shard is closed and can no longer receive more data\.

## Request Syntax<a name="API_streams_DescribeStream_RequestSyntax"></a>

```
{
   "ExclusiveStartShardId": "string",
   "Limit": number,
   "StreamArn": "string"
}
```

## Request Parameters<a name="API_streams_DescribeStream_RequestParameters"></a>

The request accepts the following data in JSON format\.

**Note**  
In the following list, the required parameters are described first\.

 ** StreamArn **   
The Amazon Resource Name \(ARN\) for the stream\.  
Type: String  
Length Constraints: Minimum length of 37\. Maximum length of 1024\.  
Required: Yes

 ** ExclusiveStartShardId **   
The shard ID of the first item that this operation will evaluate\. Use the value that was returned for `LastEvaluatedShardId` in the previous operation\.   
Type: String  
Length Constraints: Minimum length of 28\. Maximum length of 65\.  
Required: No

 ** Limit **   
The maximum number of shard objects to return\. The upper limit is 100\.  
Type: Integer  
Valid Range: Minimum value of 1\.  
Required: No

## Response Syntax<a name="API_streams_DescribeStream_ResponseSyntax"></a>

```
{
   "StreamDescription": { 
      "CreationRequestDateTime": number,
      "KeySchema": [ 
         { 
            "AttributeName": "string",
            "KeyType": "string"
         }
      ],
      "LastEvaluatedShardId": "string",
      "Shards": [ 
         { 
            "ParentShardId": "string",
            "SequenceNumberRange": { 
               "EndingSequenceNumber": "string",
               "StartingSequenceNumber": "string"
            },
            "ShardId": "string"
         }
      ],
      "StreamArn": "string",
      "StreamLabel": "string",
      "StreamStatus": "string",
      "StreamViewType": "string",
      "TableName": "string"
   }
}
```

## Response Elements<a name="API_streams_DescribeStream_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** StreamDescription **   
A complete description of the stream, including its creation date and time, the DynamoDB table associated with the stream, the shard IDs within the stream, and the beginning and ending sequence numbers of stream records within the shards\.  
Type: [StreamDescription](API_streams_StreamDescription.md) object

## Errors<a name="API_streams_DescribeStream_Errors"></a>

For information about the errors that are common to all actions, see [Common Errors](CommonErrors.md)\.

 **InternalServerError**   
An error occurred on the server side\.  
HTTP Status Code: 500

 **ResourceNotFoundException**   
The operation tried to access a nonexistent stream\.  
HTTP Status Code: 400

## Example<a name="API_streams_DescribeStream_Examples"></a>

### Describe A Stream<a name="API_streams_DescribeStream_Example_1"></a>

The following sample returns a description of a stream with a given stream ARN\. All of the shards in the stream are listed in the response, along with the beginning and ending sequence numbers of stream records within the shards\. Note that one of the shards is still open, because it does not have an `EndingSequenceNumber`\. 

#### Sample Request<a name="API_streams_DescribeStream_Example_1_Request"></a>

```
POST / HTTP/1.1
x-amzn-RequestId: <RequestID>
x-amzn-crc32: <CRC32>
Content-Type: application/x-amz-json-1.0
Content-Length: <PayloadSizeBytes>     
X-Amz-Date: <Date> 
X-Amz-Target: DynamoDBStreams_20120810.DescribeStream

{
    "StreamArn": "arn:aws:dynamodb:us-west-2:111122223333:table/Forum/stream/2015-05-20T20:51:10.252"
}
```

#### Sample Response<a name="API_streams_DescribeStream_Example_1_Response"></a>

```
HTTP/1.1 200 OK
x-amzn-RequestId: <RequestId> 
x-amz-crc32: <Checksum>
Content-Type: application/x-amz-json-1.0
Content-Length: <PayloadSizeBytes>
Date: <Date>
 
{
    "StreamDescription": {
        "StreamArn": "arn:aws:dynamodb:us-west-2:111122223333:table/Forum/stream/2015-05-20T20:51:10.252",
        "StreamLabel": "2015-05-20T20:51:10.252",
        "StreamStatus": "ENABLED",
        "StreamViewType": "NEW_AND_OLD_IMAGES",
        "CreationRequestDateTime": "Wed May 20 13:51:10 PDT 2015",
        "TableName": "Forum",
        "KeySchema": [
            {"AttributeName": "ForumName","KeyType": "HASH"},
            {"AttributeName": "Subject","KeyType": "RANGE"}
        ],
        "Shards": [
            {
                "SequenceNumberRange": {
                    "EndingSequenceNumber": "20500000000000000910398",
                    "StartingSequenceNumber": "20500000000000000910398"
                },
                "ShardId": "shardId-00000001414562045508-2bac9cd2"
            },
            {
                "ParentShardId": "shardId-00000001414562045508-2bac9cd2",
                "SequenceNumberRange": {
                    "EndingSequenceNumber": "820400000000000001192334",
                    "StartingSequenceNumber": "820400000000000001192334"
                },
                "ShardId": "shardId-00000001414576573621-f55eea83"
            },
            {
                "ParentShardId": "shardId-00000001414576573621-f55eea83",
                "SequenceNumberRange": {
                    "EndingSequenceNumber": "1683700000000000001135967",
                    "StartingSequenceNumber": "1683700000000000001135967"
                },
                "ShardId": "shardId-00000001414592258131-674fd923"
            },
            {
                "ParentShardId": "shardId-00000001414592258131-674fd923",
                "SequenceNumberRange": {"StartingSequenceNumber": "2574600000000000000935255"},
                "ShardId": "shardId-00000001414608446368-3a1afbaf"
            }
        ],
    }
}
```

## See Also<a name="API_streams_DescribeStream_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/streams-dynamodb-2012-08-10/DescribeStream) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/streams-dynamodb-2012-08-10/DescribeStream) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/streams-dynamodb-2012-08-10/DescribeStream) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/streams-dynamodb-2012-08-10/DescribeStream) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/streams-dynamodb-2012-08-10/DescribeStream) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/streams-dynamodb-2012-08-10/DescribeStream) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/streams-dynamodb-2012-08-10/DescribeStream) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/streams-dynamodb-2012-08-10/DescribeStream) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/streams-dynamodb-2012-08-10/DescribeStream) 