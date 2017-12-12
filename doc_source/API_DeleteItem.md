# DeleteItem<a name="API_DeleteItem"></a>

Deletes a single item in a table by primary key\. You can perform a conditional delete operation that deletes the item if it exists, or if it has an expected attribute value\.

In addition to deleting an item, you can also return the item's attribute values in the same operation, using the `ReturnValues` parameter\.

Unless you specify conditions, the `DeleteItem` is an idempotent operation; running it multiple times on the same item or attribute does *not* result in an error response\.

Conditional deletes are useful for deleting items only if specific conditions are met\. If those conditions are met, DynamoDB performs the delete\. Otherwise, the item is not deleted\.

## Request Syntax<a name="API_DeleteItem_RequestSyntax"></a>

```
{
   "ConditionalOperator": "string",
   "ConditionExpression": "string",
   "Expected": { 
      "string" : { 
         "AttributeValueList": [ 
            { 
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
         ],
         "ComparisonOperator": "string",
         "Exists": boolean,
         "Value": { 
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
   },
   "ExpressionAttributeNames": { 
      "string" : "string" 
   },
   "ExpressionAttributeValues": { 
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
   "ReturnConsumedCapacity": "string",
   "ReturnItemCollectionMetrics": "string",
   "ReturnValues": "string",
   "TableName": "string"
}
```

## Request Parameters<a name="API_DeleteItem_RequestParameters"></a>

The request accepts the following data in JSON format\.

**Note**  
In the following list, the required parameters are described first\.

 ** Key **   
A map of attribute names to `AttributeValue` objects, representing the primary key of the item to delete\.  
For the primary key, you must provide all of the attributes\. For example, with a simple primary key, you only need to provide a value for the partition key\. For a composite primary key, you must provide values for both the partition key and the sort key\.  
Type: String to [AttributeValue](API_AttributeValue.md) object map  
Key Length Constraints: Maximum length of 65535\.  
Required: Yes

 ** TableName **   
The name of the table from which to delete the item\.  
Type: String  
Length Constraints: Minimum length of 3\. Maximum length of 255\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

 ** ConditionalOperator **   
This is a legacy parameter\. Use `ConditionExpression` instead\. For more information, see [ConditionalOperator](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/LegacyConditionalParameters.ConditionalOperator.html) in the *Amazon DynamoDB Developer Guide*\.  
Type: String  
Valid Values:` AND | OR`   
Required: No

 ** ConditionExpression **   
A condition that must be satisfied in order for a conditional `DeleteItem` to succeed\.  
An expression can contain any of the following:  

+ Functions: `attribute_exists | attribute_not_exists | attribute_type | contains | begins_with | size` 

  These function names are case\-sensitive\.

+ Comparison operators: `= | <> | < | > | <= | >= | BETWEEN | IN ` 

+  Logical operators: `AND | OR | NOT` 
For more information on condition expressions, see [Specifying Conditions](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.SpecifyingConditions.html) in the *Amazon DynamoDB Developer Guide*\.  
Type: String  
Required: No

 ** Expected **   
This is a legacy parameter\. Use `ConditionExpression` instead\. For more information, see [Expected](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/LegacyConditionalParameters.Expected.html) in the *Amazon DynamoDB Developer Guide*\.  
Type: String to [ExpectedAttributeValue](API_ExpectedAttributeValue.md) object map  
Key Length Constraints: Maximum length of 65535\.  
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

 ** ExpressionAttributeValues **   
One or more values that can be substituted in an expression\.  
Use the **:** \(colon\) character in an expression to dereference an attribute value\. For example, suppose that you wanted to check whether the value of the *ProductStatus* attribute was one of the following:   
 `Available | Backordered | Discontinued`   
You would first need to specify `ExpressionAttributeValues` as follows:  
 `{ ":avail":{"S":"Available"}, ":back":{"S":"Backordered"}, ":disc":{"S":"Discontinued"} }`   
You could then use these values in an expression, such as this:  
 `ProductStatus IN (:avail, :back, :disc)`   
For more information on expression attribute values, see [Specifying Conditions](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.SpecifyingConditions.html) in the *Amazon DynamoDB Developer Guide*\.  
Type: String to [AttributeValue](API_AttributeValue.md) object map  
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

 ** ReturnItemCollectionMetrics **   
Determines whether item collection metrics are returned\. If set to `SIZE`, the response includes statistics about item collections, if any, that were modified during the operation are returned in the response\. If set to `NONE` \(the default\), no statistics are returned\.  
Type: String  
Valid Values:` SIZE | NONE`   
Required: No

 ** ReturnValues **   
Use `ReturnValues` if you want to get the item attributes as they appeared before they were deleted\. For `DeleteItem`, the valid values are:  

+  `NONE` \- If `ReturnValues` is not specified, or if its value is `NONE`, then nothing is returned\. \(This setting is the default for `ReturnValues`\.\)

+  `ALL_OLD` \- The content of the old item is returned\.
The `ReturnValues` parameter is used by several DynamoDB operations; however, `DeleteItem` does not recognize any values other than `NONE` or `ALL_OLD`\.
Type: String  
Valid Values:` NONE | ALL_OLD | UPDATED_OLD | ALL_NEW | UPDATED_NEW`   
Required: No

## Response Syntax<a name="API_DeleteItem_ResponseSyntax"></a>

```
{
   "Attributes": { 
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
   "ItemCollectionMetrics": { 
      "ItemCollectionKey": { 
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
      "SizeEstimateRangeGB": [ number ]
   }
}
```

## Response Elements<a name="API_DeleteItem_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** Attributes **   
A map of attribute names to `AttributeValue` objects, representing the item as it appeared before the `DeleteItem` operation\. This map appears in the response only if `ReturnValues` was specified as `ALL_OLD` in the request\.  
Type: String to [AttributeValue](API_AttributeValue.md) object map  
Key Length Constraints: Maximum length of 65535\.

 ** ConsumedCapacity **   
The capacity units consumed by the `DeleteItem` operation\. The data returned includes the total provisioned throughput consumed, along with statistics for the table and any indexes involved in the operation\. `ConsumedCapacity` is only returned if the `ReturnConsumedCapacity` parameter was specified\. For more information, see [Provisioned Throughput](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ProvisionedThroughputIntro.html) in the *Amazon DynamoDB Developer Guide*\.  
Type: [ConsumedCapacity](API_ConsumedCapacity.md) object

 ** ItemCollectionMetrics **   
Information about item collections, if any, that were affected by the `DeleteItem` operation\. `ItemCollectionMetrics` is only returned if the `ReturnItemCollectionMetrics` parameter was specified\. If the table does not have any local secondary indexes, this information is not returned in the response\.  
Each `ItemCollectionMetrics` element consists of:  

+  `ItemCollectionKey` \- The partition key value of the item collection\. This is the same as the partition key value of the item itself\.

+  `SizeEstimateRangeGB` \- An estimate of item collection size, in gigabytes\. This value is a two\-element array containing a lower bound and an upper bound for the estimate\. The estimate includes the size of all the items in the table, plus the size of all attributes projected into all of the local secondary indexes on that table\. Use this estimate to measure whether a local secondary index is approaching its size limit\.

  The estimate is subject to change over time; therefore, do not rely on the precision or accuracy of the estimate\.
Type: [ItemCollectionMetrics](API_ItemCollectionMetrics.md) object

## Errors<a name="API_DeleteItem_Errors"></a>

For information about the errors that are common to all actions, see [Common Errors](CommonErrors.md)\.

 **ConditionalCheckFailedException**   
A condition specified in the operation could not be evaluated\.  
HTTP Status Code: 400

 **InternalServerError**   
An error occurred on the server side\.  
HTTP Status Code: 500

 **ItemCollectionSizeLimitExceededException**   
An item collection is too large\. This exception is only returned for tables that have one or more local secondary indexes\.  
HTTP Status Code: 400

 **ProvisionedThroughputExceededException**   
Your request rate is too high\. The AWS SDKs for DynamoDB automatically retry requests that receive this exception\. Your request is eventually successful, unless your retry queue is too large to finish\. Reduce the frequency of requests and use exponential backoff\. For more information, go to [Error Retries and Exponential Backoff](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Programming.Errors.html#Programming.Errors.RetryAndBackoff) in the *Amazon DynamoDB Developer Guide*\.  
HTTP Status Code: 400

 **ResourceNotFoundException**   
The operation tried to access a nonexistent table or index\. The resource might not be specified correctly, or its status might not be `ACTIVE`\.  
HTTP Status Code: 400

## Example<a name="API_DeleteItem_Examples"></a>

### Delete an Item<a name="API_DeleteItem_Example_1"></a>

The following example deletes an item from the Thread table, but only if that item does not already have an attribute named *Replies*\. Because `ReturnValues` is set to `ALL_OLD`, the response contains the item as it appeared before the delete\.

#### Sample Request<a name="API_DeleteItem_Example_1_Request"></a>

```
POST / HTTP/1.1
Host: dynamodb.<region>.<domain>;
Accept-Encoding: identity
Content-Length: <PayloadSizeBytes>     
User-Agent: <UserAgentString>
Content-Type: application/x-amz-json-1.0
Authorization: AWS4-HMAC-SHA256 Credential=<Credential>, SignedHeaders=<Headers>, Signature=<Signature>
X-Amz-Date: <Date> 
X-Amz-Target: DynamoDB_20120810.DeleteItem 

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
    "ConditionExpression": "attribute_not_exists(Replies)",
    "ReturnValues": "ALL_OLD"
}
```

#### Sample Response<a name="API_DeleteItem_Example_1_Response"></a>

```
HTTP/1.1 200 OK
x-amzn-RequestId: <RequestId> 
x-amz-crc32: <Checksum>
Content-Type: application/x-amz-json-1.0
Content-Length: <PayloadSizeBytes>
Date: <Date>
 {
    "Attributes": {
        "LastPostedBy": {
            "S": "fred@example.com"
        },
        "ForumName": {
            "S": "Amazon DynamoDB"
        },
        "LastPostDateTime": {
            "S": "201303201023"
        },
        "Tags": {
            "SS": ["Update","Multiple Items","HelpMe"]
        },
        "Subject": {
            "S": "How do I update multiple items?"
        },
        "Message": {
            "S": "I want to update multiple items in a single call. What's the best way to do that?"
        }
    }
}
```

## See Also<a name="API_DeleteItem_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/dynamodb-2012-08-10/DeleteItem) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/dynamodb-2012-08-10/DeleteItem) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/DeleteItem) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/DeleteItem) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/DeleteItem) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/dynamodb-2012-08-10/DeleteItem) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/dynamodb-2012-08-10/DeleteItem) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/dynamodb-2012-08-10/DeleteItem) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/DeleteItem) 