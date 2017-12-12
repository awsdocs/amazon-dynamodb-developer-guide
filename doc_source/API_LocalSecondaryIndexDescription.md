# LocalSecondaryIndexDescription<a name="API_LocalSecondaryIndexDescription"></a>

Represents the properties of a local secondary index\.

## Contents<a name="API_LocalSecondaryIndexDescription_Contents"></a>

**Note**  
In the following list, the required parameters are described first\.

 **IndexArn**   
The Amazon Resource Name \(ARN\) that uniquely identifies the index\.  
Type: String  
Required: No

 **IndexName**   
Represents the name of the local secondary index\.  
Type: String  
Length Constraints: Minimum length of 3\. Maximum length of 255\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: No

 **IndexSizeBytes**   
The total size of the specified index, in bytes\. DynamoDB updates this value approximately every six hours\. Recent changes might not be reflected in this value\.  
Type: Long  
Required: No

 **ItemCount**   
The number of items in the specified index\. DynamoDB updates this value approximately every six hours\. Recent changes might not be reflected in this value\.  
Type: Long  
Required: No

 **KeySchema**   
The complete key schema for the local secondary index, consisting of one or more pairs of attribute names and key types:  

+  `HASH` \- partition key

+  `RANGE` \- sort key
The partition key of an item is also known as its *hash attribute*\. The term "hash attribute" derives from DynamoDB' usage of an internal hash function to evenly distribute data items across partitions, based on their partition key values\.  
The sort key of an item is also known as its *range attribute*\. The term "range attribute" derives from the way DynamoDB stores items with the same partition key physically close together, in sorted order by the sort key value\.
Type: Array of [KeySchemaElement](API_KeySchemaElement.md) objects  
Array Members: Minimum number of 1 item\. Maximum number of 2 items\.  
Required: No

 **Projection**   
Represents attributes that are copied \(projected\) from the table into the global secondary index\. These are in addition to the primary key attributes and index key attributes, which are automatically projected\.   
Type: [Projection](API_Projection.md) object  
Required: No

## See Also<a name="API_LocalSecondaryIndexDescription_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/LocalSecondaryIndexDescription) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/LocalSecondaryIndexDescription) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/LocalSecondaryIndexDescription) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/LocalSecondaryIndexDescription) 