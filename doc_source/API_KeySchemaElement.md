# KeySchemaElement<a name="API_KeySchemaElement"></a>

Represents *a single element* of a key schema\. A key schema specifies the attributes that make up the primary key of a table, or the key attributes of an index\.

A `KeySchemaElement` represents exactly one attribute of the primary key\. For example, a simple primary key would be represented by one `KeySchemaElement` \(for the partition key\)\. A composite primary key would require one `KeySchemaElement` for the partition key, and another `KeySchemaElement` for the sort key\.

A `KeySchemaElement` must be a scalar, top\-level attribute \(not a nested attribute\)\. The data type must be one of String, Number, or Binary\. The attribute cannot be nested within a List or a Map\.

## Contents<a name="API_KeySchemaElement_Contents"></a>

**Note**  
In the following list, the required parameters are described first\.

 **AttributeName**   
The name of a key attribute\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 255\.  
Required: Yes

 **KeyType**   
The role that this key attribute will assume:  

+  `HASH` \- partition key

+  `RANGE` \- sort key
The partition key of an item is also known as its *hash attribute*\. The term "hash attribute" derives from DynamoDB' usage of an internal hash function to evenly distribute data items across partitions, based on their partition key values\.  
The sort key of an item is also known as its *range attribute*\. The term "range attribute" derives from the way DynamoDB stores items with the same partition key physically close together, in sorted order by the sort key value\.
Type: String  
Valid Values:` HASH | RANGE`   
Required: Yes

## See Also<a name="API_KeySchemaElement_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/KeySchemaElement) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/KeySchemaElement) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/KeySchemaElement) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/KeySchemaElement) 