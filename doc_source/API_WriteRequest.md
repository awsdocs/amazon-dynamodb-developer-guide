# WriteRequest<a name="API_WriteRequest"></a>

Represents an operation to perform \- either `DeleteItem` or `PutItem`\. You can only request one of these operations, not both, in a single `WriteRequest`\. If you do need to perform both of these operations, you will need to provide two separate `WriteRequest` objects\.

## Contents<a name="API_WriteRequest_Contents"></a>

**Note**  
In the following list, the required parameters are described first\.

 **DeleteRequest**   
A request to perform a `DeleteItem` operation\.  
Type: [DeleteRequest](API_DeleteRequest.md) object  
Required: No

 **PutRequest**   
A request to perform a `PutItem` operation\.  
Type: [PutRequest](API_PutRequest.md) object  
Required: No

## See Also<a name="API_WriteRequest_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/WriteRequest) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/WriteRequest) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/WriteRequest) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/WriteRequest) 