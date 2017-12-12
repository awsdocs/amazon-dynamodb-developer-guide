# StreamSpecification<a name="API_StreamSpecification"></a>

Represents the DynamoDB Streams configuration for a table in DynamoDB\.

## Contents<a name="API_StreamSpecification_Contents"></a>

**Note**  
In the following list, the required parameters are described first\.

 **StreamEnabled**   
Indicates whether DynamoDB Streams is enabled \(true\) or disabled \(false\) on the table\.  
Type: Boolean  
Required: No

 **StreamViewType**   
 When an item in the table is modified, `StreamViewType` determines what information is written to the stream for this table\. Valid values for `StreamViewType` are:  

+  `KEYS_ONLY` \- Only the key attributes of the modified item are written to the stream\.

+  `NEW_IMAGE` \- The entire item, as it appears after it was modified, is written to the stream\.

+  `OLD_IMAGE` \- The entire item, as it appeared before it was modified, is written to the stream\.

+  `NEW_AND_OLD_IMAGES` \- Both the new and the old item images of the item are written to the stream\.
Type: String  
Valid Values:` NEW_IMAGE | OLD_IMAGE | NEW_AND_OLD_IMAGES | KEYS_ONLY`   
Required: No

## See Also<a name="API_StreamSpecification_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/StreamSpecification) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/StreamSpecification) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/StreamSpecification) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/StreamSpecification) 