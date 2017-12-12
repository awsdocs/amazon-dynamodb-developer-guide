# CreateGlobalSecondaryIndexAction<a name="API_CreateGlobalSecondaryIndexAction"></a>

Represents a new global secondary index to be added to an existing table\.

## Contents<a name="API_CreateGlobalSecondaryIndexAction_Contents"></a>

**Note**  
In the following list, the required parameters are described first\.

 **IndexName**   
The name of the global secondary index to be created\.  
Type: String  
Length Constraints: Minimum length of 3\. Maximum length of 255\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

 **KeySchema**   
The key schema for the global secondary index\.  
Type: Array of [KeySchemaElement](API_KeySchemaElement.md) objects  
Array Members: Minimum number of 1 item\. Maximum number of 2 items\.  
Required: Yes

 **Projection**   
Represents attributes that are copied \(projected\) from the table into an index\. These are in addition to the primary key attributes and index key attributes, which are automatically projected\.  
Type: [Projection](API_Projection.md) object  
Required: Yes

 **ProvisionedThroughput**   
Represents the provisioned throughput settings for the specified global secondary index\.  
For current minimum and maximum provisioned throughput values, see [Limits](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Limits.html) in the *Amazon DynamoDB Developer Guide*\.  
Type: [ProvisionedThroughput](API_ProvisionedThroughput.md) object  
Required: Yes

## See Also<a name="API_CreateGlobalSecondaryIndexAction_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/CreateGlobalSecondaryIndexAction) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/CreateGlobalSecondaryIndexAction) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/CreateGlobalSecondaryIndexAction) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/CreateGlobalSecondaryIndexAction) 