# GetItem<a name="API_GetItem"></a>

The `GetItem` operation returns a set of attributes for the item with the given primary key\. If there is no matching item, `GetItem` does not return any data and there will be no `Item` element in the response\.

 `GetItem` provides an eventually consistent read by default\. If your application requires a strongly consistent read, set `ConsistentRead` to `true`\. Although a strongly consistent read might take more time than an eventually consistent read, it always returns the last updated value\.

## Request Syntax<a name="API_GetItem_RequestSyntax"></a>

```
{
   "AttributesToGet": [ "string" ],
   "ConsistentRead": boolean,
   "ExpressionAttributeNames": { 
      "string" : "string" 
   },
   "Key": { 
      "string" : { 
         "B": blob,
         "BOOL": boolean,
         "BS": [ blob ],
         "L": [ 
            "AttributeValue"
         ],
         "M": { 
            "string" : "AttributeValue"
         },
         "N": "string",
         "NS": [ "string" ],
         "NULL": boolean,
         "S": "string",
         "SS": [ "string" ]
      }
   },
   "ProjectionExpression": "string",
   "ReturnConsumedCapacity": "string",
   "TableName": "string"
}
```

## Request Parameters<a name="API_GetItem_RequestParameters"></a>

The request accepts the following data in JSON format\.

**Note**  
In the following list, the required parameters are described first\.

 ** Key **   
A map of attribute names to `AttributeValue` objects, representing the primary key of the item to retrieve\.  
For the primary key, you must provide all of the attributes\. For example, with a simple primary key, you only need to provide a value for the partition key\. For a composite primary key, you must provide values for both the partition key and the sort key\.  
Type: String to [AttributeValue](API_AttributeValue.md) object map  
Key Length Constraints: Maximum length of 65535\.  
Required: Yes

 ** TableName **   
The name of the table containing the requested item\.  
Type: String  
Length Constraints: Minimum length of 3\. Maximum length of 255\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

 ** AttributesToGet **   
This is a legacy parameter\. Use `ProjectionExpression` instead\. For more information, see [AttributesToGet](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/LegacyConditionalParameters.AttributesToGet.html) in the *Amazon DynamoDB Developer Guide*\.  
Type: Array of strings  
Array Members: Minimum number of 1 item\.  
Length Constraints: Maximum length of 65535\.  
Required: No

 ** ConsistentRead **   
Determines the read consistency model: If set to `true`, then the operation uses strongly consistent reads; otherwise, the operation uses eventually consistent reads\.  
Type: Boolean  
Required: No

 ** ExpressionAttributeNames **   
One or more substitution tokens for attribute names in an expression\. The following are some use cases for using `ExpressionAttributeNames`:  

+ To access an attribute whose name conflicts with a DynamoDB reserved word\.

+ To create a placeholder for repeating occurrences of an attribute name in an expression\.

+ To prevent special characters in an attribute name from being misinterpreted in an expression\.
Use the **\#** character in an expression to dereference an attribute name\. For example, consider the following attribute name:  

+  `Percentile` 
The name of this attribute conflicts with a reserved word, so it cannot be used directly in an expression\. \(For the complete list of reserved words, see [Reserved Words](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ReservedWords.html) in the *Amazon DynamoDB Developer Guide*\)\. To work around this, you could specify the following for `ExpressionAttributeNames`:  

+  `{"#P":"Percentile"}` 
You could then use this substitution in an expression, as in this example:  

+  `#P = :val` 
Tokens that begin with the **:** character are *expression attribute values*, which are placeholders for the actual value at runtime\.
For more information on expression attribute names, see [Accessing Item Attributes](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.AccessingItemAttributes.html) in the *Amazon DynamoDB Developer Guide*\.  
Type: String to string map  
Value Length Constraints: Maximum length of 65535\.  
Required: No

 ** ProjectionExpression **   
A string that identifies one or more attributes to retrieve from the table\. These attributes can include scalars, sets, or elements of a JSON document\. The attributes in the expression must be separated by commas\.  
If no attribute names are specified, then all attributes will be returned\. If any of the requested attributes are not found, they will not appear in the result\.  
For more information, see [Accessing Item Attributes](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.AccessingItemAttributes.html) in the *Amazon DynamoDB Developer Guide*\.  
Type: String  
Required: No

 ** ReturnConsumedCapacity **   
Determines the level of detail about provisioned throughput consumption that is returned in the response:  

+  `INDEXES` \- The response includes the aggregate `ConsumedCapacity` for the operation, together with `ConsumedCapacity` for each table and secondary index that was accessed\.

  Note that some operations, such as `GetItem` and `BatchGetItem`, do not access any indexes at all\. In these cases, specifying `INDEXES` will only return `ConsumedCapacity` information for table\(s\)\.

+  `TOTAL` \- The response includes only the aggregate `ConsumedCapacity` for the operation\.

+  `NONE` \- No `ConsumedCapacity` details are included in the response\.
Type: String  
Valid Values:` INDEXES | TOTAL | NONE`   
Required: No

## Response Syntax<a name="API_GetItem_ResponseSyntax"></a>

```
{
   "ConsumedCapacity": { 
      "CapacityUnits": number,
      "GlobalSecondaryIndexes": { 
         "string" : { 
            "CapacityUnits": number
         }
      },
      "LocalSecondaryIndexes": { 
         "string" : { 
            "CapacityUnits": number
         }
      },
      "Table": { 
         "CapacityUnits": number
      },
      "TableName": "string"
   },
   "Item": { 
      "string" : { 
         "B": blob,
         "BOOL": boolean,
         "BS": [ blob ],
         "L": [ 
            "AttributeValue"
         ],
         "M": { 
            "string" : "AttributeValue"
         },
         "N": "string",
         "NS": [ "string" ],
         "NULL": boolean,
         "S": "string",
         "SS": [ "string" ]
      }
   }
}
```

## Response Elements<a name="API_GetItem_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** ConsumedCapacity **   
The capacity units consumed by the `GetItem` operation\. The data returned includes the total provisioned throughput consumed, along with statistics for the table and any indexes involved in the operation\. `ConsumedCapacity` is only returned if the `ReturnConsumedCapacity` parameter was specified\. For more information, see [Provisioned Throughput](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ProvisionedThroughputIntro.html) in the *Amazon DynamoDB Developer Guide*\.  
Type: [ConsumedCapacity](API_ConsumedCapacity.md) object

 ** Item **   
A map of attribute names to `AttributeValue` objects, as specified by `ProjectionExpression`\.  
Type: String to [AttributeValue](API_AttributeValue.md) object map  
Key Length Constraints: Maximum length of 65535\.

## Errors<a name="API_GetItem_Errors"></a>

For information about the errors that are common to all actions, see [Common Errors](CommonErrors.md)\.

 **InternalServerError**   
An error occurred on the server side\.  
HTTP Status Code: 500

 **ProvisionedThroughputExceededException**   
Your request rate is too high\. The AWS SDKs for DynamoDB automatically retry requests that receive this exception\. Your request is eventually successful, unless your retry queue is too large to finish\. Reduce the frequency of requests and use exponential backoff\. For more information, go to [Error Retries and Exponential Backoff](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Programming.Errors.html#Programming.Errors.RetryAndBackoff) in the *Amazon DynamoDB Developer Guide*\.  
HTTP Status Code: 400

 **ResourceNotFoundException**   
The operation tried to access a nonexistent table or index\. The resource might not be specified correctly, or its status might not be `ACTIVE`\.  
HTTP Status Code: 400

## Example<a name="API_GetItem_Examples"></a>

### Retrieve Item Attributes<a name="API_GetItem_Example_1"></a>

The following example retrieves three attributes from the Thread table\. In the response, the `ConsumedCapacityUnits` value is 1, because `ConsistentRead` is set to true\. If `ConsistentRead` had been set to false \(or not specified\) for the same request, an eventually consistent read would have been used and `ConsumedCapacityUnits` would have been 0\.5\.

#### Sample Request<a name="API_GetItem_Example_1_Request"></a>

```
POST / HTTP/1.1
Host: dynamodb.<region>.<domain>;
Accept-Encoding: identity
Content-Length: <PayloadSizeBytes>     
User-Agent: <UserAgentString>
Content-Type: application/x-amz-json-1.0
Authorization: AWS4-HMAC-SHA256 Credential=<Credential>, SignedHeaders=<Headers>, Signature=<Signature>
X-Amz-Date: <Date> 
X-Amz-Target: DynamoDB_20120810.GetItem 

{
    "TableName": "Thread",
    "Key": {
        "ForumName": {
            "S": "Amazon DynamoDB"
        },
        "Subject": {
            "S": "How do I update multiple items?"
        }
    },
    "ProjectionExpression":"LastPostDateTime, Message, Tags",
    "ConsistentRead": true,
    "ReturnConsumedCapacity": "TOTAL"
}
```

#### Sample Response<a name="API_GetItem_Example_1_Response"></a>

```
HTTP/1.1 200 OK
x-amzn-RequestId: <RequestId> 
x-amz-crc32: <Checksum>
Content-Type: application/x-amz-json-1.0
Content-Length: <PayloadSizeBytes>
Date: <Date>
 {
    "ConsumedCapacity": {
        "CapacityUnits": 1,
        "TableName": "Thread"
    },
    "Item": {
        "Tags": {
            "SS": ["Update","Multiple Items","HelpMe"]
        },
        "LastPostDateTime": {
            "S": "201303190436"
        },
        "Message": {
            "S": "I want to update multiple items in a single call. What's the best way to do that?"
        }
    }
}
```

## See Also<a name="API_GetItem_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/dynamodb-2012-08-10/GetItem) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/dynamodb-2012-08-10/GetItem) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/GetItem) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/GetItem) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/GetItem) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/dynamodb-2012-08-10/GetItem) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/dynamodb-2012-08-10/GetItem) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/dynamodb-2012-08-10/GetItem) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/GetItem) 