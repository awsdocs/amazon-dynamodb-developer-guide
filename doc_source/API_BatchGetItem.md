# BatchGetItem<a name="API_BatchGetItem"></a>

The `BatchGetItem` operation returns the attributes of one or more items from one or more tables\. You identify requested items by primary key\.

A single operation can retrieve up to 16 MB of data, which can contain as many as 100 items\. `BatchGetItem` will return a partial result if the response size limit is exceeded, the table's provisioned throughput is exceeded, or an internal processing failure occurs\. If a partial result is returned, the operation returns a value for `UnprocessedKeys`\. You can use this value to retry the operation starting with the next item to get\.

**Important**  
If you request more than 100 items `BatchGetItem` will return a `ValidationException` with the message "Too many items requested for the BatchGetItem call"\.

For example, if you ask to retrieve 100 items, but each individual item is 300 KB in size, the system returns 52 items \(so as not to exceed the 16 MB limit\)\. It also returns an appropriate `UnprocessedKeys` value so you can get the next page of results\. If desired, your application can include its own logic to assemble the pages of results into one data set\.

If *none* of the items can be processed due to insufficient provisioned throughput on all of the tables in the request, then `BatchGetItem` will return a `ProvisionedThroughputExceededException`\. If *at least one* of the items is successfully processed, then `BatchGetItem` completes successfully, while returning the keys of the unread items in `UnprocessedKeys`\.

**Important**  
If DynamoDB returns any unprocessed items, you should retry the batch operation on those items\. However, *we strongly recommend that you use an exponential backoff algorithm*\. If you retry the batch operation immediately, the underlying read or write requests can still fail due to throttling on the individual tables\. If you delay the batch operation using exponential backoff, the individual requests in the batch are much more likely to succeed\.  
For more information, see [Batch Operations and Error Handling](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ErrorHandling.html#BatchOperations) in the *Amazon DynamoDB Developer Guide*\.

By default, `BatchGetItem` performs eventually consistent reads on every table in the request\. If you want strongly consistent reads instead, you can set `ConsistentRead` to `true` for any or all tables\.

In order to minimize response latency, `BatchGetItem` retrieves items in parallel\.

When designing your application, keep in mind that DynamoDB does not return items in any particular order\. To help parse the response by item, include the primary key values for the items in your request in the `ProjectionExpression` parameter\.

If a requested item does not exist, it is not returned in the result\. Requests for nonexistent items consume the minimum read capacity units according to the type of read\. For more information, see [Capacity Units Calculations](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/WorkingWithTables.html#CapacityUnitCalculations) in the *Amazon DynamoDB Developer Guide*\.

## Request Syntax<a name="API_BatchGetItem_RequestSyntax"></a>

```
{
   "RequestItems": { 
      "string" : { 
         "AttributesToGet": [ "string" ],
         "ConsistentRead": boolean,
         "ExpressionAttributeNames": { 
            "string" : "string" 
         },
         "Keys": [ 
            { 
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
         ],
         "ProjectionExpression": "string"
      }
   },
   "ReturnConsumedCapacity": "string"
}
```

## Request Parameters<a name="API_BatchGetItem_RequestParameters"></a>

The request accepts the following data in JSON format\.

**Note**  
In the following list, the required parameters are described first\.

 ** RequestItems **   
A map of one or more table names and, for each table, a map that describes one or more items to retrieve from that table\. Each table name can be used only once per `BatchGetItem` request\.  
Each element in the map of items to retrieve consists of the following:  

+  `ConsistentRead` \- If `true`, a strongly consistent read is used; if `false` \(the default\), an eventually consistent read is used\.

+  `ExpressionAttributeNames` \- One or more substitution tokens for attribute names in the `ProjectionExpression` parameter\. The following are some use cases for using `ExpressionAttributeNames`:

  + To access an attribute whose name conflicts with a DynamoDB reserved word\.

  + To create a placeholder for repeating occurrences of an attribute name in an expression\.

  + To prevent special characters in an attribute name from being misinterpreted in an expression\.

  Use the **\#** character in an expression to dereference an attribute name\. For example, consider the following attribute name:

  +  `Percentile` 

  The name of this attribute conflicts with a reserved word, so it cannot be used directly in an expression\. \(For the complete list of reserved words, see [Reserved Words](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ReservedWords.html) in the *Amazon DynamoDB Developer Guide*\)\. To work around this, you could specify the following for `ExpressionAttributeNames`:

  +  `{"#P":"Percentile"}` 

  You could then use this substitution in an expression, as in this example:

  +  `#P = :val` 
**Note**  
Tokens that begin with the **:** character are *expression attribute values*, which are placeholders for the actual value at runtime\.

  For more information on expression attribute names, see [Accessing Item Attributes](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.AccessingItemAttributes.html) in the *Amazon DynamoDB Developer Guide*\.

+  `Keys` \- An array of primary key attribute values that define specific items in the table\. For each primary key, you must provide *all* of the key attributes\. For example, with a simple primary key, you only need to provide the partition key value\. For a composite key, you must provide *both* the partition key value and the sort key value\.

+  `ProjectionExpression` \- A string that identifies one or more attributes to retrieve from the table\. These attributes can include scalars, sets, or elements of a JSON document\. The attributes in the expression must be separated by commas\.

  If no attribute names are specified, then all attributes will be returned\. If any of the requested attributes are not found, they will not appear in the result\.

  For more information, see [Accessing Item Attributes](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.AccessingItemAttributes.html) in the *Amazon DynamoDB Developer Guide*\.

+  `AttributesToGet` \- This is a legacy parameter\. Use `ProjectionExpression` instead\. For more information, see [AttributesToGet](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/LegacyConditionalParameters.AttributesToGet.html) in the *Amazon DynamoDB Developer Guide*\. 
Type: String to [KeysAndAttributes](API_KeysAndAttributes.md) object map  
Key Length Constraints: Minimum length of 3\. Maximum length of 255\.  
Key Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

 ** ReturnConsumedCapacity **   
Determines the level of detail about provisioned throughput consumption that is returned in the response:  

+  `INDEXES` \- The response includes the aggregate `ConsumedCapacity` for the operation, together with `ConsumedCapacity` for each table and secondary index that was accessed\.

  Note that some operations, such as `GetItem` and `BatchGetItem`, do not access any indexes at all\. In these cases, specifying `INDEXES` will only return `ConsumedCapacity` information for table\(s\)\.

+  `TOTAL` \- The response includes only the aggregate `ConsumedCapacity` for the operation\.

+  `NONE` \- No `ConsumedCapacity` details are included in the response\.
Type: String  
Valid Values:` INDEXES | TOTAL | NONE`   
Required: No

## Response Syntax<a name="API_BatchGetItem_ResponseSyntax"></a>

```
{
   "ConsumedCapacity": [ 
      { 
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
      }
   ],
   "Responses": { 
      "string" : [ 
         { 
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
      ]
   },
   "UnprocessedKeys": { 
      "string" : { 
         "AttributesToGet": [ "string" ],
         "ConsistentRead": boolean,
         "ExpressionAttributeNames": { 
            "string" : "string" 
         },
         "Keys": [ 
            { 
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
         ],
         "ProjectionExpression": "string"
      }
   }
}
```

## Response Elements<a name="API_BatchGetItem_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** ConsumedCapacity **   
The read capacity units consumed by the entire `BatchGetItem` operation\.  
Each element consists of:  

+  `TableName` \- The table that consumed the provisioned throughput\.

+  `CapacityUnits` \- The total number of capacity units consumed\.
Type: Array of [ConsumedCapacity](API_ConsumedCapacity.md) objects

 ** Responses **   
A map of table name to a list of items\. Each object in `Responses` consists of a table name, along with a map of attribute data consisting of the data type and attribute value\.  
Type: String to array of string to [AttributeValue](API_AttributeValue.md) object maps map  
Key Length Constraints: Minimum length of 3\. Maximum length of 255\.  
Key Pattern: `[a-zA-Z0-9_.-]+`   
Key Length Constraints: Maximum length of 65535\.

 ** UnprocessedKeys **   
A map of tables and their respective keys that were not processed with the current response\. The `UnprocessedKeys` value is in the same form as `RequestItems`, so the value can be provided directly to a subsequent `BatchGetItem` operation\. For more information, see `RequestItems` in the Request Parameters section\.  
Each element consists of:  

+  `Keys` \- An array of primary key attribute values that define specific items in the table\.

+  `ProjectionExpression` \- One or more attributes to be retrieved from the table or index\. By default, all attributes are returned\. If a requested attribute is not found, it does not appear in the result\.

+  `ConsistentRead` \- The consistency of a read operation\. If set to `true`, then a strongly consistent read is used; otherwise, an eventually consistent read is used\.
If there are no unprocessed keys remaining, the response contains an empty `UnprocessedKeys` map\.  
Type: String to [KeysAndAttributes](API_KeysAndAttributes.md) object map  
Key Length Constraints: Minimum length of 3\. Maximum length of 255\.  
Key Pattern: `[a-zA-Z0-9_.-]+` 

## Errors<a name="API_BatchGetItem_Errors"></a>

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

## Example<a name="API_BatchGetItem_Examples"></a>

### Retrieve Items From Multiple Tables<a name="API_BatchGetItem_Example_1"></a>

The following sample requests attributes from two different tables\.

#### Sample Request<a name="API_BatchGetItem_Example_1_Request"></a>

```
POST / HTTP/1.1
Host: dynamodb.<region>.<domain>;
Accept-Encoding: identity
Content-Length: <PayloadSizeBytes>     
User-Agent: <UserAgentString>
Content-Type: application/x-amz-json-1.0
Authorization: AWS4-HMAC-SHA256 Credential=<Credential>, SignedHeaders=<Headers>, Signature=<Signature>
X-Amz-Date: <Date> 
X-Amz-Target: DynamoDB_20120810.BatchGetItem 

{
    "RequestItems": {
        "Forum": {
            "Keys": [
                {
                    "Name":{"S":"Amazon DynamoDB"}
                },
                {
                    "Name":{"S":"Amazon RDS"}
                },
                {
                    "Name":{"S":"Amazon Redshift"}
                }
            ],
            "ProjectionExpression":"Name, Threads, Messages, Views"
        },
        "Thread": {
            "Keys": [
                {
                    "ForumName":{"S":"Amazon DynamoDB"},
                    "Subject":{"S":"Concurrent reads"}
                }
            ],
            "ProjectionExpression":"Tags, Message"
        }
    },
    "ReturnConsumedCapacity": "TOTAL"
}
```

#### Sample Response<a name="API_BatchGetItem_Example_1_Response"></a>

```
HTTP/1.1 200 OK
x-amzn-RequestId: <RequestId> 
x-amz-crc32: <Checksum>
Content-Type: application/x-amz-json-1.0
Content-Length: <PayloadSizeBytes>
Date: <Date>
 {
    "Responses": {
        "Forum": [
            {
                "Name":{
                    "S":"Amazon DynamoDB"
                },
                "Threads":{
                    "N":"5"
                },
                "Messages":{
                    "N":"19"
                },
                "Views":{
                    "N":"35"
                }
            },
            {
                "Name":{
                    "S":"Amazon RDS"
                },
                "Threads":{
                    "N":"8"
                },
                "Messages":{
                    "N":"32"
                },
                "Views":{
                    "N":"38"
                }
            },
            {
                "Name":{
                    "S":"Amazon Redshift"
                },
                "Threads":{
                    "N":"12"
                },
                "Messages":{
                    "N":"55"
                },
                "Views":{
                    "N":"47"
                }
            }
        ]
        "Thread": [
            {
                "Tags":{
                    "SS":["Reads","MultipleUsers"]
                },
                "Message":{
                    "S":"How many users can read a single data item at a time? Are there any limits?"
                }
            }
        ]
    },
    "UnprocessedKeys": {
    },
    "ConsumedCapacity": [
        {
            "TableName": "Forum",
            "CapacityUnits": 3
        },
        {
            "TableName": "Thread",
            "CapacityUnits": 1
        }
    ]
}
```

## See Also<a name="API_BatchGetItem_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/dynamodb-2012-08-10/BatchGetItem) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/dynamodb-2012-08-10/BatchGetItem) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/BatchGetItem) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/BatchGetItem) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/BatchGetItem) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/dynamodb-2012-08-10/BatchGetItem) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/dynamodb-2012-08-10/BatchGetItem) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/dynamodb-2012-08-10/BatchGetItem) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/BatchGetItem) 