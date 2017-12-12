# GlobalSecondaryIndexUpdate<a name="API_GlobalSecondaryIndexUpdate"></a>

Represents one of the following:

+ A new global secondary index to be added to an existing table\.

+ New provisioned throughput parameters for an existing global secondary index\.

+ An existing global secondary index to be removed from an existing table\.

## Contents<a name="API_GlobalSecondaryIndexUpdate_Contents"></a>

**Note**  
In the following list, the required parameters are described first\.

 **Create**   
The parameters required for creating a global secondary index on an existing table:  

+  `IndexName ` 

+  `KeySchema ` 

+  `AttributeDefinitions ` 

+  `Projection ` 

+  `ProvisionedThroughput ` 
Type: [CreateGlobalSecondaryIndexAction](API_CreateGlobalSecondaryIndexAction.md) object  
Required: No

 **Delete**   
The name of an existing global secondary index to be removed\.  
Type: [DeleteGlobalSecondaryIndexAction](API_DeleteGlobalSecondaryIndexAction.md) object  
Required: No

 **Update**   
The name of an existing global secondary index, along with new provisioned throughput settings to be applied to that index\.  
Type: [UpdateGlobalSecondaryIndexAction](API_UpdateGlobalSecondaryIndexAction.md) object  
Required: No

## See Also<a name="API_GlobalSecondaryIndexUpdate_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/GlobalSecondaryIndexUpdate) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/GlobalSecondaryIndexUpdate) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/GlobalSecondaryIndexUpdate) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/GlobalSecondaryIndexUpdate) 