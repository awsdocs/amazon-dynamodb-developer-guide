# Shard<a name="API_streams_Shard"></a>

A uniquely identified group of stream records within a stream\.

## Contents<a name="API_streams_Shard_Contents"></a>

**Note**  
In the following list, the required parameters are described first\.

 **ParentShardId**   
The shard ID of the current shard's parent\.  
Type: String  
Length Constraints: Minimum length of 28\. Maximum length of 65\.  
Required: No

 **SequenceNumberRange**   
The range of possible sequence numbers for the shard\.  
Type: [SequenceNumberRange](API_streams_SequenceNumberRange.md) object  
Required: No

 **ShardId**   
The system\-generated identifier for this shard\.  
Type: String  
Length Constraints: Minimum length of 28\. Maximum length of 65\.  
Required: No

## See Also<a name="API_streams_Shard_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/streams-dynamodb-2012-08-10/Shard) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/streams-dynamodb-2012-08-10/Shard) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/streams-dynamodb-2012-08-10/Shard) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/streams-dynamodb-2012-08-10/Shard) 