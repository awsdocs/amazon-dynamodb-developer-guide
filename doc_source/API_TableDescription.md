# TableDescription<a name="API_TableDescription"></a>

Represents the properties of a table\.

## Contents<a name="API_TableDescription_Contents"></a>

**Note**  
In the following list, the required parameters are described first\.

 **AttributeDefinitions**   
An array of `AttributeDefinition` objects\. Each of these objects describes one attribute in the table and index key schema\.  
Each `AttributeDefinition` object in this array is composed of:  

+  `AttributeName` \- The name of the attribute\.

+  `AttributeType` \- The data type for the attribute\.
Type: Array of [AttributeDefinition](API_AttributeDefinition.md) objects  
Required: No

 **CreationDateTime**   
The date and time when the table was created, in [UNIX epoch time](http://www.epochconverter.com/) format\.  
Type: Timestamp  
Required: No

 **GlobalSecondaryIndexes**   
The global secondary indexes, if any, on the table\. Each index is scoped to a given partition key value\. Each element is composed of:  

+  `Backfilling` \- If true, then the index is currently in the backfilling phase\. Backfilling occurs only when a new global secondary index is added to the table; it is the process by which DynamoDB populates the new index with data from the table\. \(This attribute does not appear for indexes that were created during a `CreateTable` operation\.\)

+  `IndexName` \- The name of the global secondary index\.

+  `IndexSizeBytes` \- The total size of the global secondary index, in bytes\. DynamoDB updates this value approximately every six hours\. Recent changes might not be reflected in this value\. 

+  `IndexStatus` \- The current status of the global secondary index:

  +  `CREATING` \- The index is being created\.

  +  `UPDATING` \- The index is being updated\.

  +  `DELETING` \- The index is being deleted\.

  +  `ACTIVE` \- The index is ready for use\.

+  `ItemCount` \- The number of items in the global secondary index\. DynamoDB updates this value approximately every six hours\. Recent changes might not be reflected in this value\. 

+  `KeySchema` \- Specifies the complete index key schema\. The attribute names in the key schema must be between 1 and 255 characters \(inclusive\)\. The key schema must begin with the same partition key as the table\.

+  `Projection` \- Specifies attributes that are copied \(projected\) from the table into the index\. These are in addition to the primary key attributes and index key attributes, which are automatically projected\. Each attribute specification is composed of:

  +  `ProjectionType` \- One of the following:

    +  `KEYS_ONLY` \- Only the index and primary keys are projected into the index\.

    +  `INCLUDE` \- Only the specified table attributes are projected into the index\. The list of projected attributes are in `NonKeyAttributes`\.

    +  `ALL` \- All of the table attributes are projected into the index\.

  +  `NonKeyAttributes` \- A list of one or more non\-key attribute names that are projected into the secondary index\. The total count of attributes provided in `NonKeyAttributes`, summed across all of the secondary indexes, must not exceed 20\. If you project the same attribute into two different indexes, this counts as two distinct attributes when determining the total\.

+  `ProvisionedThroughput` \- The provisioned throughput settings for the global secondary index, consisting of read and write capacity units, along with data about increases and decreases\. 
If the table is in the `DELETING` state, no information about indexes will be returned\.  
Type: Array of [GlobalSecondaryIndexDescription](API_GlobalSecondaryIndexDescription.md) objects  
Required: No

 **ItemCount**   
The number of items in the specified table\. DynamoDB updates this value approximately every six hours\. Recent changes might not be reflected in this value\.  
Type: Long  
Required: No

 **KeySchema**   
The primary key structure for the table\. Each `KeySchemaElement` consists of:  

+  `AttributeName` \- The name of the attribute\.

+  `KeyType` \- The role of the attribute:

  +  `HASH` \- partition key

  +  `RANGE` \- sort key
**Note**  
The partition key of an item is also known as its *hash attribute*\. The term "hash attribute" derives from DynamoDB' usage of an internal hash function to evenly distribute data items across partitions, based on their partition key values\.  
The sort key of an item is also known as its *range attribute*\. The term "range attribute" derives from the way DynamoDB stores items with the same partition key physically close together, in sorted order by the sort key value\.
For more information about primary keys, see [Primary Key](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DataModel.html#DataModelPrimaryKey) in the *Amazon DynamoDB Developer Guide*\.  
Type: Array of [KeySchemaElement](API_KeySchemaElement.md) objects  
Array Members: Minimum number of 1 item\. Maximum number of 2 items\.  
Required: No

 **LatestStreamArn**   
The Amazon Resource Name \(ARN\) that uniquely identifies the latest stream for this table\.  
Type: String  
Length Constraints: Minimum length of 37\. Maximum length of 1024\.  
Required: No

 **LatestStreamLabel**   
A timestamp, in ISO 8601 format, for this stream\.  
Note that `LatestStreamLabel` is not a unique identifier for the stream, because it is possible that a stream from another table might have the same timestamp\. However, the combination of the following three elements is guaranteed to be unique:  

+ the AWS customer ID\.

+ the table name\.

+ the `StreamLabel`\.
Type: String  
Required: No

 **LocalSecondaryIndexes**   
Represents one or more local secondary indexes on the table\. Each index is scoped to a given partition key value\. Tables with one or more local secondary indexes are subject to an item collection size limit, where the amount of data within a given item collection cannot exceed 10 GB\. Each element is composed of:  

+  `IndexName` \- The name of the local secondary index\.

+  `KeySchema` \- Specifies the complete index key schema\. The attribute names in the key schema must be between 1 and 255 characters \(inclusive\)\. The key schema must begin with the same partition key as the table\.

+  `Projection` \- Specifies attributes that are copied \(projected\) from the table into the index\. These are in addition to the primary key attributes and index key attributes, which are automatically projected\. Each attribute specification is composed of:

  +  `ProjectionType` \- One of the following:

    +  `KEYS_ONLY` \- Only the index and primary keys are projected into the index\.

    +  `INCLUDE` \- Only the specified table attributes are projected into the index\. The list of projected attributes are in `NonKeyAttributes`\.

    +  `ALL` \- All of the table attributes are projected into the index\.

  +  `NonKeyAttributes` \- A list of one or more non\-key attribute names that are projected into the secondary index\. The total count of attributes provided in `NonKeyAttributes`, summed across all of the secondary indexes, must not exceed 20\. If you project the same attribute into two different indexes, this counts as two distinct attributes when determining the total\.

+  `IndexSizeBytes` \- Represents the total size of the index, in bytes\. DynamoDB updates this value approximately every six hours\. Recent changes might not be reflected in this value\.

+  `ItemCount` \- Represents the number of items in the index\. DynamoDB updates this value approximately every six hours\. Recent changes might not be reflected in this value\.
If the table is in the `DELETING` state, no information about indexes will be returned\.  
Type: Array of [LocalSecondaryIndexDescription](API_LocalSecondaryIndexDescription.md) objects  
Required: No

 **ProvisionedThroughput**   
The provisioned throughput settings for the table, consisting of read and write capacity units, along with data about increases and decreases\.  
Type: [ProvisionedThroughputDescription](API_ProvisionedThroughputDescription.md) object  
Required: No

 **RestoreSummary**   
Contains details for the restore\.  
Type: [RestoreSummary](API_RestoreSummary.md) object  
Required: No

 **StreamSpecification**   
The current DynamoDB Streams configuration for the table\.  
Type: [StreamSpecification](API_StreamSpecification.md) object  
Required: No

 **TableArn**   
The Amazon Resource Name \(ARN\) that uniquely identifies the table\.  
Type: String  
Required: No

 **TableId**   
Unique identifier for the table for which the backup was created\.   
Type: String  
Pattern: `[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}`   
Required: No

 **TableName**   
The name of the table\.  
Type: String  
Length Constraints: Minimum length of 3\. Maximum length of 255\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: No

 **TableSizeBytes**   
The total size of the specified table, in bytes\. DynamoDB updates this value approximately every six hours\. Recent changes might not be reflected in this value\.  
Type: Long  
Required: No

 **TableStatus**   
The current state of the table:  

+  `CREATING` \- The table is being created\.

+  `UPDATING` \- The table is being updated\.

+  `DELETING` \- The table is being deleted\.

+  `ACTIVE` \- The table is ready for use\.
Type: String  
Valid Values:` CREATING | UPDATING | DELETING | ACTIVE`   
Required: No

## See Also<a name="API_TableDescription_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/TableDescription) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/TableDescription) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/TableDescription) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/TableDescription) 