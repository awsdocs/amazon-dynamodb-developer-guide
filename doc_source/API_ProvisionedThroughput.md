# ProvisionedThroughput<a name="API_ProvisionedThroughput"></a>

Represents the provisioned throughput settings for a specified table or index\. The settings can be modified using the `UpdateTable` operation\.

For current minimum and maximum provisioned throughput values, see [Limits](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Limits.html) in the *Amazon DynamoDB Developer Guide*\.

## Contents<a name="API_ProvisionedThroughput_Contents"></a>

**Note**  
In the following list, the required parameters are described first\.

 **ReadCapacityUnits**   
The maximum number of strongly consistent reads consumed per second before DynamoDB returns a `ThrottlingException`\. For more information, see [Specifying Read and Write Requirements](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/WorkingWithTables.html#ProvisionedThroughput) in the *Amazon DynamoDB Developer Guide*\.  
Type: Long  
Valid Range: Minimum value of 1\.  
Required: Yes

 **WriteCapacityUnits**   
The maximum number of writes consumed per second before DynamoDB returns a `ThrottlingException`\. For more information, see [Specifying Read and Write Requirements](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/WorkingWithTables.html#ProvisionedThroughput) in the *Amazon DynamoDB Developer Guide*\.  
Type: Long  
Valid Range: Minimum value of 1\.  
Required: Yes

## See Also<a name="API_ProvisionedThroughput_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/ProvisionedThroughput) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/ProvisionedThroughput) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/ProvisionedThroughput) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/ProvisionedThroughput) 