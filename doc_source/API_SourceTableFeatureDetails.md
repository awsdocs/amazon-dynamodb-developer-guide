# SourceTableFeatureDetails<a name="API_SourceTableFeatureDetails"></a>

Contains the details of the features enabled on the table when the backup was created\. For example, LSIs, GSIs, streams, TTL\. 

## Contents<a name="API_SourceTableFeatureDetails_Contents"></a>

**Note**  
In the following list, the required parameters are described first\.

 **GlobalSecondaryIndexes**   
Represents the GSI properties for the table when the backup was created\. It includes the IndexName, KeySchema, Projection and ProvisionedThroughput for the GSIs on the table at the time of backup\.   
Type: Array of [GlobalSecondaryIndexInfo](API_GlobalSecondaryIndexInfo.md) objects  
Required: No

 **LocalSecondaryIndexes**   
Represents the LSI properties for the table when the backup was created\. It includes the IndexName, KeySchema and Projection for the LSIs on the table at the time of backup\.   
Type: Array of [LocalSecondaryIndexInfo](API_LocalSecondaryIndexInfo.md) objects  
Required: No

 **StreamDescription**   
Stream settings on the table when the backup was created\.  
Type: [StreamSpecification](API_StreamSpecification.md) object  
Required: No

 **TimeToLiveDescription**   
Time to Live settings on the table when the backup was created\.  
Type: [TimeToLiveDescription](API_TimeToLiveDescription.md) object  
Required: No

## See Also<a name="API_SourceTableFeatureDetails_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/SourceTableFeatureDetails) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/SourceTableFeatureDetails) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/SourceTableFeatureDetails) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/SourceTableFeatureDetails) 