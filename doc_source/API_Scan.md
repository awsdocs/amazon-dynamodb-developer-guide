# Scan<a name="API_Scan"></a>

The `Scan` operation returns one or more items and item attributes by accessing every item in a table or a secondary index\. To have DynamoDB return fewer items, you can provide a `FilterExpression` operation\.

If the total number of scanned items exceeds the maximum data set size limit of 1 MB, the scan stops and results are returned to the user as a `LastEvaluatedKey` value to continue the scan in a subsequent operation\. The results also include the number of items exceeding the limit\. A scan can result in no table data meeting the filter criteria\. 

A single `Scan` operation will read up to the maximum number of items set \(if using the `Limit` parameter\) or a maximum of 1 MB of data and then apply any filtering to the results using `FilterExpression`\. If `LastEvaluatedKey` is present in the response, you will need to paginate the result set\. For more information, see [Paginating the Results](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Scan.html#Scan.Pagination) in the *Amazon DynamoDB Developer Guide*\. 

 `Scan` operations proceed sequentially; however, for faster performance on a large table or secondary index, applications can request a parallel `Scan` operation by providing the `Segment` and `TotalSegments` parameters\. For more information, see [Parallel Scan](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Scan.html#Scan.ParallelScan) in the *Amazon DynamoDB Developer Guide*\.

 `Scan` uses eventually consistent reads when accessing the data in a table; therefore, the result set might not include the changes to data in the table immediately before the operation began\. If you need a consistent copy of the data, as of the time that the `Scan` begins, you can set the `ConsistentRead` parameter to `true`\.

## Request Syntax<a name="API_Scan_RequestSyntax"></a>

```
{
   "AttributesToGet": [ "string" ],
   "ConditionalOperator": "string",
   "ConsistentRead": boolean,
   "ExclusiveStartKey": { 
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
   "FilterExpression": "string",
   "IndexName": "string",
   "Limit": number,
   "ProjectionExpression": "string",
   "ReturnConsumedCapacity": "string",
   "ScanFilter": { 
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
         "ComparisonOperator": "string"
      }
   },
   "Segment": number,
   "Select": "string",
   "TableName": "string",
   "TotalSegments": number
}
```

## Request Parameters<a name="API_Scan_RequestParameters"></a>

The request accepts the following data in JSON format\.

**Note**  
In the following list, the required parameters are described first\.

 ** TableName **   
The name of the table containing the requested items; or, if you provide `IndexName`, the name of the table to which that index belongs\.  
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

 ** ConditionalOperator **   
This is a legacy parameter\. Use `FilterExpression` instead\. For more information, see [ConditionalOperator](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/LegacyConditionalParameters.ConditionalOperator.html) in the *Amazon DynamoDB Developer Guide*\.  
Type: String  
Valid Values:` AND | OR`   
Required: No

 ** ConsistentRead **   
A Boolean value that determines the read consistency model during the scan:  

+ If `ConsistentRead` is `false`, then the data returned from `Scan` might not contain the results from other recently completed write operations \(PutItem, UpdateItem or DeleteItem\)\.

+ If `ConsistentRead` is `true`, then all of the write operations that completed before the `Scan` began are guaranteed to be contained in the `Scan` response\.
The default setting for `ConsistentRead` is `false`\.  
The `ConsistentRead` parameter is not supported on global secondary indexes\. If you scan a global secondary index with `ConsistentRead` set to true, you will receive a `ValidationException`\.  
Type: Boolean  
Required: No

 ** ExclusiveStartKey **   
The primary key of the first item that this operation will evaluate\. Use the value that was returned for `LastEvaluatedKey` in the previous operation\.  
The data type for `ExclusiveStartKey` must be String, Number or Binary\. No set data types are allowed\.  
In a parallel scan, a `Scan` request that includes `ExclusiveStartKey` must specify the same segment whose previous `Scan` returned the corresponding value of `LastEvaluatedKey`\.  
Type: String to [AttributeValue](API_AttributeValue.md) object map  
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

 ** FilterExpression **   
A string that contains conditions that DynamoDB applies after the `Scan` operation, but before the data is returned to you\. Items that do not satisfy the `FilterExpression` criteria are not returned\.  
A `FilterExpression` is applied after the items have already been read; the process of filtering does not consume any additional read capacity units\.
For more information, see [Filter Expressions](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/QueryAndScan.html#FilteringResults) in the *Amazon DynamoDB Developer Guide*\.  
Type: String  
Required: No

 ** IndexName **   
The name of a secondary index to scan\. This index can be any local secondary index or global secondary index\. Note that if you use the `IndexName` parameter, you must also provide `TableName`\.  
Type: String  
Length Constraints: Minimum length of 3\. Maximum length of 255\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: No

 ** Limit **   
The maximum number of items to evaluate \(not necessarily the number of matching items\)\. If DynamoDB processes the number of items up to the limit while processing the results, it stops the operation and returns the matching values up to that point, and a key in `LastEvaluatedKey` to apply in a subsequent operation, so that you can pick up where you left off\. Also, if the processed data set size exceeds 1 MB before DynamoDB reaches this limit, it stops the operation and returns the matching values up to the limit, and a key in `LastEvaluatedKey` to apply in a subsequent operation to continue the operation\. For more information, see [Query and Scan](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/QueryAndScan.html) in the *Amazon DynamoDB Developer Guide*\.  
Type: Integer  
Valid Range: Minimum value of 1\.  
Required: No

 ** ProjectionExpression **   
A string that identifies one or more attributes to retrieve from the specified table or index\. These attributes can include scalars, sets, or elements of a JSON document\. The attributes in the expression must be separated by commas\.  
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

 ** ScanFilter **   
This is a legacy parameter\. Use `FilterExpression` instead\. For more information, see [ScanFilter](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/LegacyConditionalParameters.ScanFilter.html) in the *Amazon DynamoDB Developer Guide*\.  
Type: String to [Condition](API_Condition.md) object map  
Key Length Constraints: Maximum length of 65535\.  
Required: No

 ** Segment **   
For a parallel `Scan` request, `Segment` identifies an individual segment to be scanned by an application worker\.  
Segment IDs are zero\-based, so the first segment is always 0\. For example, if you want to use four application threads to scan a table or an index, then the first thread specifies a `Segment` value of 0, the second thread specifies 1, and so on\.  
The value of `LastEvaluatedKey` returned from a parallel `Scan` request must be used as `ExclusiveStartKey` with the same segment ID in a subsequent `Scan` operation\.  
The value for `Segment` must be greater than or equal to 0, and less than the value provided for `TotalSegments`\.  
If you provide `Segment`, you must also provide `TotalSegments`\.  
Type: Integer  
Valid Range: Minimum value of 0\. Maximum value of 999999\.  
Required: No

 ** Select **   
The attributes to be returned in the result\. You can retrieve all item attributes, specific item attributes, the count of matching items, or in the case of an index, some or all of the attributes projected into the index\.  

+  `ALL_ATTRIBUTES` \- Returns all of the item attributes from the specified table or index\. If you query a local secondary index, then for each matching item in the index DynamoDB will fetch the entire item from the parent table\. If the index is configured to project all item attributes, then all of the data can be obtained from the local secondary index, and no fetching is required\.

+  `ALL_PROJECTED_ATTRIBUTES` \- Allowed only when querying an index\. Retrieves all attributes that have been projected into the index\. If the index is configured to project all attributes, this return value is equivalent to specifying `ALL_ATTRIBUTES`\.

+  `COUNT` \- Returns the number of matching items, rather than the matching items themselves\.

+  `SPECIFIC_ATTRIBUTES` \- Returns only the attributes listed in `AttributesToGet`\. This return value is equivalent to specifying `AttributesToGet` without specifying any value for `Select`\.

  If you query or scan a local secondary index and request only attributes that are projected into that index, the operation will read only the index and not the table\. If any of the requested attributes are not projected into the local secondary index, DynamoDB will fetch each of these attributes from the parent table\. This extra fetching incurs additional throughput cost and latency\.

  If you query or scan a global secondary index, you can only request attributes that are projected into the index\. Global secondary index queries cannot fetch attributes from the parent table\.
If neither `Select` nor `AttributesToGet` are specified, DynamoDB defaults to `ALL_ATTRIBUTES` when accessing a table, and `ALL_PROJECTED_ATTRIBUTES` when accessing an index\. You cannot use both `Select` and `AttributesToGet` together in a single request, unless the value for `Select` is `SPECIFIC_ATTRIBUTES`\. \(This usage is equivalent to specifying `AttributesToGet` without any value for `Select`\.\)  
If you use the `ProjectionExpression` parameter, then the value for `Select` can only be `SPECIFIC_ATTRIBUTES`\. Any other value for `Select` will return an error\.
Type: String  
Valid Values:` ALL_ATTRIBUTES | ALL_PROJECTED_ATTRIBUTES | SPECIFIC_ATTRIBUTES | COUNT`   
Required: No

 ** TotalSegments **   
For a parallel `Scan` request, `TotalSegments` represents the total number of segments into which the `Scan` operation will be divided\. The value of `TotalSegments` corresponds to the number of application workers that will perform the parallel scan\. For example, if you want to use four application threads to scan a table or an index, specify a `TotalSegments` value of 4\.  
The value for `TotalSegments` must be greater than or equal to 1, and less than or equal to 1000000\. If you specify a `TotalSegments` value of 1, the `Scan` operation will be sequential rather than parallel\.  
If you specify `TotalSegments`, you must also specify `Segment`\.  
Type: Integer  
Valid Range: Minimum value of 1\. Maximum value of 1000000\.  
Required: No

## Response Syntax<a name="API_Scan_ResponseSyntax"></a>

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
   "Count": number,
   "Items": [ 
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
   "LastEvaluatedKey": { 
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
   "ScannedCount": number
}
```

## Response Elements<a name="API_Scan_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** ConsumedCapacity **   
The capacity units consumed by the `Scan` operation\. The data returned includes the total provisioned throughput consumed, along with statistics for the table and any indexes involved in the operation\. `ConsumedCapacity` is only returned if the `ReturnConsumedCapacity` parameter was specified\. For more information, see [Provisioned Throughput](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ProvisionedThroughputIntro.html) in the *Amazon DynamoDB Developer Guide*\.  
Type: [ConsumedCapacity](API_ConsumedCapacity.md) object

 ** Count **   
The number of items in the response\.  
If you set `ScanFilter` in the request, then `Count` is the number of items returned after the filter was applied, and `ScannedCount` is the number of matching items before the filter was applied\.  
If you did not use a filter in the request, then `Count` is the same as `ScannedCount`\.  
Type: Integer

 ** Items **   
An array of item attributes that match the scan criteria\. Each element in this array consists of an attribute name and the value for that attribute\.  
Type: Array of string to [AttributeValue](API_AttributeValue.md) object maps  
Key Length Constraints: Maximum length of 65535\.

 ** LastEvaluatedKey **   
The primary key of the item where the operation stopped, inclusive of the previous result set\. Use this value to start a new operation, excluding this value in the new request\.  
If `LastEvaluatedKey` is empty, then the "last page" of results has been processed and there is no more data to be retrieved\.  
If `LastEvaluatedKey` is not empty, it does not necessarily mean that there is more data in the result set\. The only way to know when you have reached the end of the result set is when `LastEvaluatedKey` is empty\.  
Type: String to [AttributeValue](API_AttributeValue.md) object map  
Key Length Constraints: Maximum length of 65535\.

 ** ScannedCount **   
The number of items evaluated, before any `ScanFilter` is applied\. A high `ScannedCount` value with few, or no, `Count` results indicates an inefficient `Scan` operation\. For more information, see [Count and ScannedCount](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/QueryAndScan.html#Count) in the *Amazon DynamoDB Developer Guide*\.  
If you did not use a filter in the request, then `ScannedCount` is the same as `Count`\.  
Type: Integer

## Errors<a name="API_Scan_Errors"></a>

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

## Examples<a name="API_Scan_Examples"></a>

### Return All Items<a name="API_Scan_Example_1"></a>

The following example returns all of the items in a table\. No scan filter is applied\.

#### Sample Request<a name="API_Scan_Example_1_Request"></a>

```
POST / HTTP/1.1
Host: dynamodb.<region>.<domain>;
Accept-Encoding: identity
Content-Length: <PayloadSizeBytes>     
User-Agent: <UserAgentString>
Content-Type: application/x-amz-json-1.0
Authorization: AWS4-HMAC-SHA256 Credential=<Credential>, SignedHeaders=<Headers>, Signature=<Signature>
X-Amz-Date: <Date> 
X-Amz-Target: DynamoDB_20120810.Scan 

{
    "TableName": "Reply",
    "ReturnConsumedCapacity": "TOTAL"
}
```

#### Sample Response<a name="API_Scan_Example_1_Response"></a>

```
HTTP/1.1 200 OK
x-amzn-RequestId: <RequestId> 
x-amz-crc32: <Checksum>
Content-Type: application/x-amz-json-1.0
Content-Length: <PayloadSizeBytes>
Date: <Date>
 {
    "ConsumedCapacity": {
        "CapacityUnits": 0.5,
        "TableName": "Reply"
    },
    "Count": 4,
    "Items": [
        {
            "PostedBy": {
                "S": "joe@example.com"
            },
            "ReplyDateTime": {
                "S": "20130320115336"
            },
            "Id": {
                "S": "Amazon DynamoDB#How do I update multiple items?"
            },
            "Message": {
                "S": "Have you looked at BatchWriteItem?"
            }
        },
        {
            "PostedBy": {
                "S": "fred@example.com"
            },
            "ReplyDateTime": {
                "S": "20130320115342"
            },
            "Id": {
                "S": "Amazon DynamoDB#How do I update multiple items?"
            },
            "Message": {
                "S": "No, I didn't know about that.  Where can I find more information?"
            }
        },
        {
            "PostedBy": {
                "S": "joe@example.com"
            },
            "ReplyDateTime": {
                "S": "20130320115347"
            },
            "Id": {
                "S": "Amazon DynamoDB#How do I update multiple items?"
            },
            "Message": {
                "S": "BatchWriteItem is documented in the Amazon DynamoDB API Reference."
            }
        },
        {
            "PostedBy": {
                "S": "fred@example.com"
            },
            "ReplyDateTime": {
                "S": "20130320115352"
            },
            "Id": {
                "S": "Amazon DynamoDB#How do I update multiple items?"
            },
            "Message": {
                "S": "OK, I'll take a look at that.  Thanks!"
            }
        }
    ],
    "ScannedCount": 4
}
```

### Use a Filter Expression<a name="API_Scan_Example_2"></a>

The following example returns only those items matching specific criteria\.

#### Sample Request<a name="API_Scan_Example_2_Request"></a>

```
POST / HTTP/1.1
Host: dynamodb.<region>.<domain>;
Accept-Encoding: identity
Content-Length: <PayloadSizeBytes>     
User-Agent: <UserAgentString>
Content-Type: application/x-amz-json-1.0
Authorization: AWS4-HMAC-SHA256 Credential=<Credential>, SignedHeaders=<Headers>, Signature=<Signature>
X-Amz-Date: <Date> 
X-Amz-Target: DynamoDB_20120810.Scan 

{
    "TableName": "Reply",
    "FilterExpression": "PostedBy = :val",
    "ExpressionAttributeValues": {":val": {"S": "joe@example.com"}},
    "ReturnConsumedCapacity": "TOTAL"
}
```

#### Sample Response<a name="API_Scan_Example_2_Response"></a>

```
HTTP/1.1 200 OK
x-amzn-RequestId: <RequestId> 
x-amz-crc32: <Checksum>
Content-Type: application/x-amz-json-1.0
Content-Length: <PayloadSizeBytes>
Date: <Date>
 {
    "ConsumedCapacity": {
        "CapacityUnits": 0.5,
        "TableName": "Reply"
    },
    "Count": 2,
    "Items": [
        {
            "PostedBy": {
                "S": "joe@example.com"
            },
            "ReplyDateTime": {
                "S": "20130320115336"
            },
            "Id": {
                "S": "Amazon DynamoDB#How do I update multiple items?"
            },
            "Message": {
                "S": "Have you looked at BatchWriteItem?"
            }
        },
        {
            "PostedBy": {
                "S": "joe@example.com"
            },
            "ReplyDateTime": {
                "S": "20130320115347"
            },
            "Id": {
                "S": "Amazon DynamoDB#How do I update multiple items?"
            },
            "Message": {
                "S": "BatchWriteItem is documented in the Amazon DynamoDB API Reference."
            }
        }
    ],
    "ScannedCount": 4
}
```

## See Also<a name="API_Scan_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/dynamodb-2012-08-10/Scan) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/dynamodb-2012-08-10/Scan) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/Scan) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/Scan) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/Scan) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/dynamodb-2012-08-10/Scan) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/dynamodb-2012-08-10/Scan) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/dynamodb-2012-08-10/Scan) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/Scan) 