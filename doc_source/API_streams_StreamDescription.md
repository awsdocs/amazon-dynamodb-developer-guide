# StreamDescription<a name="API_streams_StreamDescription"></a>

Represents all of the data describing a particular stream\.

## Contents<a name="API_streams_StreamDescription_Contents"></a>

**Note**  
In the following list, the required parameters are described first\.

 **CreationRequestDateTime**   
The date and time when the request to create this stream was issued\.  
Type: Timestamp  
Required: No

 **KeySchema**   
The key attribute\(s\) of the stream's DynamoDB table\.  
Type: Array of [KeySchemaElement](API_streams_KeySchemaElement.md) objects  
Array Members: Minimum number of 1 item\. Maximum number of 2 items\.  
Required: No

 **LastEvaluatedShardId**   
The shard ID of the item where the operation stopped, inclusive of the previous result set\. Use this value to start a new operation, excluding this value in the new request\.  
If `LastEvaluatedShardId` is empty, then the "last page" of results has been processed and there is currently no more data to be retrieved\.  
If `LastEvaluatedShardId` is not empty, it does not necessarily mean that there is more data in the result set\. The only way to know when you have reached the end of the result set is when `LastEvaluatedShardId` is empty\.  
Type: String  
Length Constraints: Minimum length of 28\. Maximum length of 65\.  
Required: No

 **Shards**   
The shards that comprise the stream\.  
Type: Array of [Shard](API_streams_Shard.md) objects  
Required: No

 **StreamArn**   
The Amazon Resource Name \(ARN\) for the stream\.  
Type: String  
Length Constraints: Minimum length of 37\. Maximum length of 1024\.  
Required: No

 **StreamLabel**   
A timestamp, in ISO 8601 format, for this stream\.  
Note that `LatestStreamLabel` is not a unique identifier for the stream, because it is possible that a stream from another table might have the same timestamp\. However, the combination of the following three elements is guaranteed to be unique:  

+ the AWS customer ID\.

+ the table name

+ the `StreamLabel` 
Type: String  
Required: No

 **StreamStatus**   
Indicates the current status of the stream:  

+  `ENABLING` \- Streams is currently being enabled on the DynamoDB table\.

+  `ENABLED` \- the stream is enabled\.

+  `DISABLING` \- Streams is currently being disabled on the DynamoDB table\.

+  `DISABLED` \- the stream is disabled\.
Type: String  
Valid Values:` ENABLING | ENABLED | DISABLING | DISABLED`   
Required: No

 **StreamViewType**   
Indicates the format of the records within this stream:  

+  `KEYS_ONLY` \- only the key attributes of items that were modified in the DynamoDB table\.

+  `NEW_IMAGE` \- entire items from the table, as they appeared after they were modified\.

+  `OLD_IMAGE` \- entire items from the table, as they appeared before they were modified\.

+  `NEW_AND_OLD_IMAGES` \- both the new and the old images of the items from the table\.
Type: String  
Valid Values:` NEW_IMAGE | OLD_IMAGE | NEW_AND_OLD_IMAGES | KEYS_ONLY`   
Required: No

 **TableName**   
The DynamoDB table with which the stream is associated\.  
Type: String  
Length Constraints: Minimum length of 3\. Maximum length of 255\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: No

## See Also<a name="API_streams_StreamDescription_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/streams-dynamodb-2012-08-10/StreamDescription) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/streams-dynamodb-2012-08-10/StreamDescription) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/streams-dynamodb-2012-08-10/StreamDescription) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/streams-dynamodb-2012-08-10/StreamDescription) 