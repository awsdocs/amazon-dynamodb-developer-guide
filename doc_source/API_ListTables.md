# ListTables<a name="API_ListTables"></a>

Returns an array of table names associated with the current account and endpoint\. The output from `ListTables` is paginated, with each page returning a maximum of 100 table names\.

## Request Syntax<a name="API_ListTables_RequestSyntax"></a>

```
{
   "ExclusiveStartTableName": "string",
   "Limit": number
}
```

## Request Parameters<a name="API_ListTables_RequestParameters"></a>

The request accepts the following data in JSON format\.

**Note**  
In the following list, the required parameters are described first\.

 ** ExclusiveStartTableName **   
The first table name that this operation will evaluate\. Use the value that was returned for `LastEvaluatedTableName` in a previous operation, so that you can obtain the next page of results\.  
Type: String  
Length Constraints: Minimum length of 3\. Maximum length of 255\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: No

 ** Limit **   
A maximum number of table names to return\. If this parameter is not specified, the limit is 100\.  
Type: Integer  
Valid Range: Minimum value of 1\. Maximum value of 100\.  
Required: No

## Response Syntax<a name="API_ListTables_ResponseSyntax"></a>

```
{
   "LastEvaluatedTableName": "string",
   "TableNames": [ "string" ]
}
```

## Response Elements<a name="API_ListTables_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** LastEvaluatedTableName **   
The name of the last table in the current page of results\. Use this value as the `ExclusiveStartTableName` in a new request to obtain the next page of results, until all the table names are returned\.  
If you do not receive a `LastEvaluatedTableName` value in the response, this means that there are no more table names to be retrieved\.  
Type: String  
Length Constraints: Minimum length of 3\. Maximum length of 255\.  
Pattern: `[a-zA-Z0-9_.-]+` 

 ** TableNames **   
The names of the tables associated with the current account at the current endpoint\. The maximum size of this array is 100\.  
If `LastEvaluatedTableName` also appears in the output, you can use this value as the `ExclusiveStartTableName` parameter in a subsequent `ListTables` request and obtain the next page of results\.  
Type: Array of strings  
Length Constraints: Minimum length of 3\. Maximum length of 255\.  
Pattern: `[a-zA-Z0-9_.-]+` 

## Errors<a name="API_ListTables_Errors"></a>

For information about the errors that are common to all actions, see [Common Errors](CommonErrors.md)\.

 **InternalServerError**   
An error occurred on the server side\.  
HTTP Status Code: 500

## Example<a name="API_ListTables_Examples"></a>

### List Tables<a name="API_ListTables_Example_1"></a>

This example requests a list of tables, starting with a table named *Forum* and ending after three table names have been returned\.

#### Sample Request<a name="API_ListTables_Example_1_Request"></a>

```
POST / HTTP/1.1
Host: dynamodb.<region>.<domain>;
Accept-Encoding: identity
Content-Length: <PayloadSizeBytes>     
User-Agent: <UserAgentString>
Content-Type: application/x-amz-json-1.0
Authorization: AWS4-HMAC-SHA256 Credential=<Credential>, SignedHeaders=<Headers>, Signature=<Signature>
X-Amz-Date: <Date> 
X-Amz-Target: DynamoDB_20120810.ListTables 

{
    "ExclusiveStartTableName": "Forum",
    "Limit": 3
}
```

#### Sample Response<a name="API_ListTables_Example_1_Response"></a>

```
HTTP/1.1 200 OK
x-amzn-RequestId: <RequestId> 
x-amz-crc32: <Checksum>
Content-Type: application/x-amz-json-1.0
Content-Length: <PayloadSizeBytes>
Date: <Date>
 {
    "LastEvaluatedTableName": "Thread",
    "TableNames": ["Forum","Reply","Thread"]
}
```

## See Also<a name="API_ListTables_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/dynamodb-2012-08-10/ListTables) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/dynamodb-2012-08-10/ListTables) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/ListTables) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/ListTables) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/ListTables) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/dynamodb-2012-08-10/ListTables) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/dynamodb-2012-08-10/ListTables) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/dynamodb-2012-08-10/ListTables) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/ListTables) 