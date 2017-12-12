# GetRecords<a name="API_streams_GetRecords"></a>

Retrieves the stream records from a given shard\.

Specify a shard iterator using the `ShardIterator` parameter\. The shard iterator specifies the position in the shard from which you want to start reading stream records sequentially\. If there are no stream records available in the portion of the shard that the iterator points to, `GetRecords` returns an empty list\. Note that it might take multiple calls to get to a portion of the shard that contains stream records\.

**Note**  
 `GetRecords` can retrieve a maximum of 1 MB of data or 1000 stream records, whichever comes first\.

## Request Syntax<a name="API_streams_GetRecords_RequestSyntax"></a>

```
{
   "Limit": number,
   "ShardIterator": "string"
}
```

## Request Parameters<a name="API_streams_GetRecords_RequestParameters"></a>

The request accepts the following data in JSON format\.

**Note**  
In the following list, the required parameters are described first\.

 ** ShardIterator **   
A shard iterator that was retrieved from a previous GetShardIterator operation\. This iterator can be used to access the stream records in this shard\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Required: Yes

 ** Limit **   
The maximum number of records to return from the shard\. The upper limit is 1000\.  
Type: Integer  
Valid Range: Minimum value of 1\.  
Required: No

## Response Syntax<a name="API_streams_GetRecords_ResponseSyntax"></a>

```
{
   "NextShardIterator": "string",
   "Records": [ 
      { 
         "awsRegion": "string",
         "dynamodb": { 
            "ApproximateCreationDateTime": number,
            "Keys": { 
               "string" : { 
                  "B": blob,
                  "BOOL": boolean,
                  "BS": [ blob ],
                  "L": [ 
                     "AttributeValue"
                  ],
                  "M": { 
                     "string" : "AttributeValue"
                  },
                  "N": "string",
                  "NS": [ "string" ],
                  "NULL": boolean,
                  "S": "string",
                  "SS": [ "string" ]
               }
            },
            "NewImage": { 
               "string" : { 
                  "B": blob,
                  "BOOL": boolean,
                  "BS": [ blob ],
                  "L": [ 
                     "AttributeValue"
                  ],
                  "M": { 
                     "string" : "AttributeValue"
                  },
                  "N": "string",
                  "NS": [ "string" ],
                  "NULL": boolean,
                  "S": "string",
                  "SS": [ "string" ]
               }
            },
            "OldImage": { 
               "string" : { 
                  "B": blob,
                  "BOOL": boolean,
                  "BS": [ blob ],
                  "L": [ 
                     "AttributeValue"
                  ],
                  "M": { 
                     "string" : "AttributeValue"
                  },
                  "N": "string",
                  "NS": [ "string" ],
                  "NULL": boolean,
                  "S": "string",
                  "SS": [ "string" ]
               }
            },
            "SequenceNumber": "string",
            "SizeBytes": number,
            "StreamViewType": "string"
         },
         "eventID": "string",
         "eventName": "string",
         "eventSource": "string",
         "eventVersion": "string",
         "userIdentity": { 
            "PrincipalId": "string",
            "Type": "string"
         }
      }
   ]
}
```

## Response Elements<a name="API_streams_GetRecords_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** NextShardIterator **   
The next position in the shard from which to start sequentially reading stream records\. If set to `null`, the shard has been closed and the requested iterator will not return any more data\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.

 ** Records **   
The stream records from the shard, which were retrieved using the shard iterator\.  
Type: Array of [Record](API_streams_Record.md) objects

## Errors<a name="API_streams_GetRecords_Errors"></a>

For information about the errors that are common to all actions, see [Common Errors](CommonErrors.md)\.

 **ExpiredIteratorException**   
The shard iterator has expired and can no longer be used to retrieve stream records\. A shard iterator expires 15 minutes after it is retrieved using the `GetShardIterator` action\.  
HTTP Status Code: 400

 **InternalServerError**   
An error occurred on the server side\.  
HTTP Status Code: 500

 **LimitExceededException**   
Your request rate is too high\. The AWS SDKs for DynamoDB automatically retry requests that receive this exception\. Your request is eventually successful, unless your retry queue is too large to finish\. Reduce the frequency of requests and use exponential backoff\. For more information, go to [Error Retries and Exponential Backoff](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ErrorHandling.html#APIRetries) in the *Amazon DynamoDB Developer Guide*\.  
HTTP Status Code: 400

 **ResourceNotFoundException**   
The operation tried to access a nonexistent stream\.  
HTTP Status Code: 400

 **TrimmedDataAccessException**   
The operation attempted to read past the oldest stream record in a shard\.  
In DynamoDB Streams, there is a 24 hour limit on data retention\. Stream records whose age exceeds this limit are subject to removal \(trimming\) from the stream\. You might receive a TrimmedDataAccessException if:  

+ You request a shard iterator with a sequence number older than the trim point \(24 hours\)\.

+ You obtain a shard iterator, but before you use the iterator in a `GetRecords` request, a stream record in the shard exceeds the 24 hour period and is trimmed\. This causes the iterator to access a record that no longer exists\.
HTTP Status Code: 400

## Example<a name="API_streams_GetRecords_Examples"></a>

### Retrieve stream records from a shard<a name="API_streams_GetRecords_Example_1"></a>

The following sample retrieves all the stream records from a shard\. To do this, it uses a `ShardIterator` that was obtained from a previous `GetShardIterator` call\.

#### Sample Request<a name="API_streams_GetRecords_Example_1_Request"></a>

```
POST / HTTP/1.1
x-amzn-RequestId: <RequestID>
x-amzn-crc32: <CRC32>
Content-Type: application/x-amz-json-1.0
Content-Length: <PayloadSizeBytes>     
X-Amz-Date: <Date> 
X-Amz-Target: DynamoDBStreams_20120810.GetRecords

{
    "ShardIterator": "arn:aws:dynamodb:us-west-2:111122223333:table/Forum/stream/2015-05-20T20:51:10.252|1|AAAAAAAAAAEvJp6D+zaQ...  <remaining characters omitted> ..."
}
```

#### Sample Response<a name="API_streams_GetRecords_Example_1_Response"></a>

```
HTTP/1.1 200 OK
x-amzn-RequestId: <RequestId> 
x-amz-crc32: <Checksum>
Content-Type: application/x-amz-json-1.0
Content-Length: <PayloadSizeBytes>
Date: <Date>
 
{
    "NextShardIterator": "arn:aws:dynamodb:us-west-2:111122223333:table/Forum/stream/2015-05-20T20:51:10.252|1|AAAAAAAAAAGQBYshYDEe ... <remaining characters omitted> ...",
    "Records": [
        {
            "awsRegion": "us-west-2",
            "dynamodb": {
                "ApproximateCreationDateTime": 1.46480431E9,
                "Keys": {
                    "ForumName": {"S": "DynamoDB"},
                    "Subject": {"S": "DynamoDB Thread 3"}
                },
                "SequenceNumber": "300000000000000499659",
                "SizeBytes": 41,
                "StreamViewType": "KEYS_ONLY"
            },
            "eventID": "e2fd9c34eff2d779b297b26f5fef4206",
            "eventName": "INSERT",
            "eventSource": "aws:dynamodb",
            "eventVersion": "1.0"
        },
        {
            "awsRegion": "us-west-2",
            "dynamodb": {
                "ApproximateCreationDateTime": 1.46480527E9,
                "Keys": {
                    "ForumName": {"S": "DynamoDB"},
                    "Subject": {"S": "DynamoDB Thread 1"}
                },
                "SequenceNumber": "400000000000000499660",
                "SizeBytes": 41,
                "StreamViewType": "KEYS_ONLY"
            },
            "eventID": "4b25bd0da9a181a155114127e4837252",
            "eventName": "MODIFY",
            "eventSource": "aws:dynamodb",
            "eventVersion": "1.0"
        },
        {
            "awsRegion": "us-west-2",
            "dynamodb": {
                "ApproximateCreationDateTime": 1.46480646E9,
                "Keys": {
                    "ForumName": {"S": "DynamoDB"},
                    "Subject": {"S": "DynamoDB Thread 2"}
                },
                "SequenceNumber": "500000000000000499661",
                "SizeBytes": 41,
                "StreamViewType": "KEYS_ONLY"
            },
            "eventID": "740280c73a3df7842edab3548a1b08ad",
            "eventName": "REMOVE",
            "eventSource": "aws:dynamodb",
            "eventVersion": "1.0"
        }
    ]
}
```

## See Also<a name="API_streams_GetRecords_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/streams-dynamodb-2012-08-10/GetRecords) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/streams-dynamodb-2012-08-10/GetRecords) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/streams-dynamodb-2012-08-10/GetRecords) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/streams-dynamodb-2012-08-10/GetRecords) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/streams-dynamodb-2012-08-10/GetRecords) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/streams-dynamodb-2012-08-10/GetRecords) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/streams-dynamodb-2012-08-10/GetRecords) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/streams-dynamodb-2012-08-10/GetRecords) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/streams-dynamodb-2012-08-10/GetRecords) 