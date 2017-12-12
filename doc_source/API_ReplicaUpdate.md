# ReplicaUpdate<a name="API_ReplicaUpdate"></a>

Represents one of the following:

+ A new replica to be added to an existing global table\.

+ New parameters for an existing replica\.

+ An existing replica to be removed from an existing global table\.

## Contents<a name="API_ReplicaUpdate_Contents"></a>

**Note**  
In the following list, the required parameters are described first\.

 **Create**   
The parameters required for creating a replica on an existing global table\.  
Type: [CreateReplicaAction](API_CreateReplicaAction.md) object  
Required: No

 **Delete**   
The name of the existing replica to be removed\.  
Type: [DeleteReplicaAction](API_DeleteReplicaAction.md) object  
Required: No

## See Also<a name="API_ReplicaUpdate_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/ReplicaUpdate) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/ReplicaUpdate) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/ReplicaUpdate) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/ReplicaUpdate) 