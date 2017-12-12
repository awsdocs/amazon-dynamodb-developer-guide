# ProvisionedThroughputDescription<a name="API_ProvisionedThroughputDescription"></a>

Represents the provisioned throughput settings for the table, consisting of read and write capacity units, along with data about increases and decreases\.

## Contents<a name="API_ProvisionedThroughputDescription_Contents"></a>

**Note**  
In the following list, the required parameters are described first\.

 **LastDecreaseDateTime**   
The date and time of the last provisioned throughput decrease for this table\.  
Type: Timestamp  
Required: No

 **LastIncreaseDateTime**   
The date and time of the last provisioned throughput increase for this table\.  
Type: Timestamp  
Required: No

 **NumberOfDecreasesToday**   
The number of provisioned throughput decreases for this table during this UTC calendar day\. For current maximums on provisioned throughput decreases, see [Limits](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Limits.html) in the *Amazon DynamoDB Developer Guide*\.  
Type: Long  
Valid Range: Minimum value of 1\.  
Required: No

 **ReadCapacityUnits**   
The maximum number of strongly consistent reads consumed per second before DynamoDB returns a `ThrottlingException`\. Eventually consistent reads require less effort than strongly consistent reads, so a setting of 50 `ReadCapacityUnits` per second provides 100 eventually consistent `ReadCapacityUnits` per second\.  
Type: Long  
Valid Range: Minimum value of 1\.  
Required: No

 **WriteCapacityUnits**   
The maximum number of writes consumed per second before DynamoDB returns a `ThrottlingException`\.  
Type: Long  
Valid Range: Minimum value of 1\.  
Required: No

## See Also<a name="API_ProvisionedThroughputDescription_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/ProvisionedThroughputDescription) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/ProvisionedThroughputDescription) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/ProvisionedThroughputDescription) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/ProvisionedThroughputDescription) 