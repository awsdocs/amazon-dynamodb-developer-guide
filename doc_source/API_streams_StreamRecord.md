# StreamRecord<a name="API_streams_StreamRecord"></a>

A description of a single data modification that was performed on an item in a DynamoDB table\.

## Contents<a name="API_streams_StreamRecord_Contents"></a>

**Note**  
In the following list, the required parameters are described first\.

 **ApproximateCreationDateTime**   
The approximate date and time when the stream record was created, in [UNIX epoch time](http://www.epochconverter.com/) format\.  
Type: Timestamp  
Required: No

 **Keys**   
The primary key attribute\(s\) for the DynamoDB item that was modified\.  
Type: String to [AttributeValue](API_streams_AttributeValue.md) object map  
Key Length Constraints: Maximum length of 65535\.  
Required: No

 **NewImage**   
The item in the DynamoDB table as it appeared after it was modified\.  
Type: String to [AttributeValue](API_streams_AttributeValue.md) object map  
Key Length Constraints: Maximum length of 65535\.  
Required: No

 **OldImage**   
The item in the DynamoDB table as it appeared before it was modified\.  
Type: String to [AttributeValue](API_streams_AttributeValue.md) object map  
Key Length Constraints: Maximum length of 65535\.  
Required: No

 **SequenceNumber**   
The sequence number of the stream record\.  
Type: String  
Length Constraints: Minimum length of 21\. Maximum length of 40\.  
Required: No

 **SizeBytes**   
The size of the stream record, in bytes\.  
Type: Long  
Valid Range: Minimum value of 1\.  
Required: No

 **StreamViewType**   
The type of data from the modified DynamoDB item that was captured in this stream record:  

+  `KEYS_ONLY` \- only the key attributes of the modified item\.

+  `NEW_IMAGE` \- the entire item, as it appeared after it was modified\.

+  `OLD_IMAGE` \- the entire item, as it appeared before it was modified\.

+  `NEW_AND_OLD_IMAGES` \- both the new and the old item images of the item\.
Type: String  
Valid Values:` NEW_IMAGE | OLD_IMAGE | NEW_AND_OLD_IMAGES | KEYS_ONLY`   
Required: No

## See Also<a name="API_streams_StreamRecord_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/streams-dynamodb-2012-08-10/StreamRecord) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/streams-dynamodb-2012-08-10/StreamRecord) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/streams-dynamodb-2012-08-10/StreamRecord) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/streams-dynamodb-2012-08-10/StreamRecord) 