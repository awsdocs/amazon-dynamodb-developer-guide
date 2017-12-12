# GlobalTableDescription<a name="API_GlobalTableDescription"></a>

Contains details about the global table\.

## Contents<a name="API_GlobalTableDescription_Contents"></a>

**Note**  
In the following list, the required parameters are described first\.

 **CreationDateTime**   
The creation time of the global table\.  
Type: Timestamp  
Required: No

 **GlobalTableArn**   
The unique identifier of the global table\.  
Type: String  
Required: No

 **GlobalTableName**   
The global table name\.  
Type: String  
Length Constraints: Minimum length of 3\. Maximum length of 255\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: No

 **GlobalTableStatus**   
The current state of the global table:  

+  `CREATING` \- The global table is being created\.

+  `UPDATING` \- The global table is being updated\.

+  `DELETING` \- The global table is being deleted\.

+  `ACTIVE` \- The global table is ready for use\.
Type: String  
Valid Values:` CREATING | ACTIVE | DELETING | UPDATING`   
Required: No

 **ReplicationGroup**   
The regions where the global table has replicas\.  
Type: Array of [ReplicaDescription](API_ReplicaDescription.md) objects  
Required: No

## See Also<a name="API_GlobalTableDescription_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/GlobalTableDescription) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/GlobalTableDescription) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/GlobalTableDescription) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/GlobalTableDescription) 