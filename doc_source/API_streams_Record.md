# Record<a name="API_streams_Record"></a>

A description of a unique event within a stream\.

## Contents<a name="API_streams_Record_Contents"></a>

**Note**  
In the following list, the required parameters are described first\.

 **awsRegion**   
The region in which the `GetRecords` request was received\.  
Type: String  
Required: No

 **dynamodb**   
The main body of the stream record, containing all of the DynamoDB\-specific fields\.  
Type: [StreamRecord](API_streams_StreamRecord.md) object  
Required: No

 **eventID**   
A globally unique identifier for the event that was recorded in this stream record\.  
Type: String  
Required: No

 **eventName**   
The type of data modification that was performed on the DynamoDB table:  

+  `INSERT` \- a new item was added to the table\.

+  `MODIFY` \- one or more of an existing item's attributes were modified\.

+  `REMOVE` \- the item was deleted from the table
Type: String  
Valid Values:` INSERT | MODIFY | REMOVE`   
Required: No

 **eventSource**   
The AWS service from which the stream record originated\. For DynamoDB Streams, this is `aws:dynamodb`\.  
Type: String  
Required: No

 **eventVersion**   
The version number of the stream record format\. This number is updated whenever the structure of `Record` is modified\.  
Client applications must not assume that `eventVersion` will remain at a particular value, as this number is subject to change at any time\. In general, `eventVersion` will only increase as the low\-level DynamoDB Streams API evolves\.  
Type: String  
Required: No

 **userIdentity**   
Items that are deleted by the Time to Live process after expiration have the following fields:   

+ Records\[\]\.userIdentity\.type

  "Service"

+ Records\[\]\.userIdentity\.principalId

  "dynamodb\.amazonaws\.com"
Type: [Identity](API_streams_Identity.md) object  
Required: No

## See Also<a name="API_streams_Record_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/streams-dynamodb-2012-08-10/Record) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/streams-dynamodb-2012-08-10/Record) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/streams-dynamodb-2012-08-10/Record) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/streams-dynamodb-2012-08-10/Record) 