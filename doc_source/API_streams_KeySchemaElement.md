# KeySchemaElement<a name="API_streams_KeySchemaElement"></a>

Represents *a single element* of a key schema\. A key schema specifies the attributes that make up the primary key of a table, or the key attributes of an index\.

A `KeySchemaElement` represents exactly one attribute of the primary key\. For example, a simple primary key \(partition key\) would be represented by one `KeySchemaElement`\. A composite primary key \(partition key and sort key\) would require one `KeySchemaElement` for the partition key, and another `KeySchemaElement` for the sort key\.

**Note**  
The partition key of an item is also known as its *hash attribute*\. The term "hash attribute" derives from DynamoDB's usage of an internal hash function to evenly distribute data items across partitions, based on their partition key values\.  
The sort key of an item is also known as its *range attribute*\. The term "range attribute" derives from the way DynamoDB stores items with the same partition key physically close together, in sorted order by the sort key value\.

## Contents<a name="API_streams_KeySchemaElement_Contents"></a>

**Note**  
In the following list, the required parameters are described first\.

 **AttributeName**   
The name of a key attribute\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 255\.  
Required: Yes

 **KeyType**   
The attribute data, consisting of the data type and the attribute value itself\.  
Type: String  
Valid Values:` HASH | RANGE`   
Required: Yes

## See Also<a name="API_streams_KeySchemaElement_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/streams-dynamodb-2012-08-10/KeySchemaElement) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/streams-dynamodb-2012-08-10/KeySchemaElement) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/streams-dynamodb-2012-08-10/KeySchemaElement) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/streams-dynamodb-2012-08-10/KeySchemaElement) 