# ConsumedCapacity<a name="API_ConsumedCapacity"></a>

The capacity units consumed by an operation\. The data returned includes the total provisioned throughput consumed, along with statistics for the table and any indexes involved in the operation\. `ConsumedCapacity` is only returned if the request asked for it\. For more information, see [Provisioned Throughput](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ProvisionedThroughputIntro.html) in the *Amazon DynamoDB Developer Guide*\.

## Contents<a name="API_ConsumedCapacity_Contents"></a>

**Note**  
In the following list, the required parameters are described first\.

 **CapacityUnits**   
The total number of capacity units consumed by the operation\.  
Type: Double  
Required: No

 **GlobalSecondaryIndexes**   
The amount of throughput consumed on each global index affected by the operation\.  
Type: String to [Capacity](API_Capacity.md) object map  
Key Length Constraints: Minimum length of 3\. Maximum length of 255\.  
Key Pattern: `[a-zA-Z0-9_.-]+`   
Required: No

 **LocalSecondaryIndexes**   
The amount of throughput consumed on each local index affected by the operation\.  
Type: String to [Capacity](API_Capacity.md) object map  
Key Length Constraints: Minimum length of 3\. Maximum length of 255\.  
Key Pattern: `[a-zA-Z0-9_.-]+`   
Required: No

 **Table**   
The amount of throughput consumed on the table affected by the operation\.  
Type: [Capacity](API_Capacity.md) object  
Required: No

 **TableName**   
The name of the table that was affected by the operation\.  
Type: String  
Length Constraints: Minimum length of 3\. Maximum length of 255\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: No

## See Also<a name="API_ConsumedCapacity_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/ConsumedCapacity) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/ConsumedCapacity) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/ConsumedCapacity) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/ConsumedCapacity) 