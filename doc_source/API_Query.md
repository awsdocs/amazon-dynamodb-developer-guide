# Query<a name="API_Query"></a>

The `Query` operation finds items based on primary key values\. You can query any table or secondary index that has a composite primary key \(a partition key and a sort key\)\. 

Use the `KeyConditionExpression` parameter to provide a specific value for the partition key\. The `Query` operation will return all of the items from the table or index with that partition key value\. You can optionally narrow the scope of the `Query` operation by specifying a sort key value and a comparison operator in `KeyConditionExpression`\. To further refine the `Query` results, you can optionally provide a `FilterExpression`\. A `FilterExpression` determines which items within the results should be returned to you\. All of the other results are discarded\. 

 A `Query` operation always returns a result set\. If no matching items are found, the result set will be empty\. Queries that do not return results consume the minimum number of read capacity units for that type of read operation\. 

**Note**  
 DynamoDB calculates the number of read capacity units consumed based on item size, not on the amount of data that is returned to an application\. The number of capacity units consumed will be the same whether you request all of the attributes \(the default behavior\) or just some of them \(using a projection expression\)\. The number will also be the same whether or not you use a `FilterExpression`\. 

 `Query` results are always sorted by the sort key value\. If the data type of the sort key is Number, the results are returned in numeric order; otherwise, the results are returned in order of UTF\-8 bytes\. By default, the sort order is ascending\. To reverse the order, set the `ScanIndexForward` parameter to false\. 

 A single `Query` operation will read up to the maximum number of items set \(if using the `Limit` parameter\) or a maximum of 1 MB of data and then apply any filtering to the results using `FilterExpression`\. If `LastEvaluatedKey` is present in the response, you will need to paginate the result set\. For more information, see [Paginating the Results](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Query.html#Query.Pagination) in the *Amazon DynamoDB Developer Guide*\. 

 `FilterExpression` is applied after a `Query` finishes, but before the results are returned\. A `FilterExpression` cannot contain partition key or sort key attributes\. You need to specify those attributes in the `KeyConditionExpression`\. 

**Note**  
 A `Query` operation can return an empty result set and a `LastEvaluatedKey` if all the items read for the page of results are filtered out\. 

You can query a table, a local secondary index, or a global secondary index\. For a query on a table or on a local secondary index, you can set the `ConsistentRead` parameter to `true` and obtain a strongly consistent result\. Global secondary indexes support eventually consistent reads only, so do not specify `ConsistentRead` when querying a global secondary index\.

## Request Syntax<a name="API_Query_RequestSyntax"></a>

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
   "KeyConditionExpression": "string",
   "KeyConditions": { 
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
   "Limit": number,
   "ProjectionExpression": "string",
   "QueryFilter": { 
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
   "ReturnConsumedCapacity": "string",
   "ScanIndexForward": boolean,
   "Select": "string",
   "TableName": "string"
}
```

## Request Parameters<a name="API_Query_RequestParameters"></a>

The request accepts the following data in JSON format\.

**Note**  
In the following list, the required parameters are described first\.

 ** TableName **   
The name of the table containing the requested items\.  
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
Determines the read consistency model: If set to `true`, then the operation uses strongly consistent reads; otherwise, the operation uses eventually consistent reads\.  
Strongly consistent reads are not supported on global secondary indexes\. If you query a global secondary index with `ConsistentRead` set to `true`, you will receive a `ValidationException`\.  
Type: Boolean  
Required: No

 ** ExclusiveStartKey **   
The primary key of the first item that this operation will evaluate\. Use the value that was returned for `LastEvaluatedKey` in the previous operation\.  
The data type for `ExclusiveStartKey` must be String, Number or Binary\. No set data types are allowed\.  
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
A string that contains conditions that DynamoDB applies after the `Query` operation, but before the data is returned to you\. Items that do not satisfy the `FilterExpression` criteria are not returned\.  
A `FilterExpression` does not allow key attributes\. You cannot define a filter expression based on a partition key or a sort key\.  
A `FilterExpression` is applied after the items have already been read; the process of filtering does not consume any additional read capacity units\.
For more information, see [Filter Expressions](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/QueryAndScan.html#FilteringResults) in the *Amazon DynamoDB Developer Guide*\.  
Type: String  
Required: No

 ** IndexName **   
The name of an index to query\. This index can be any local secondary index or global secondary index on the table\. Note that if you use the `IndexName` parameter, you must also provide `TableName.`   
Type: String  
Length Constraints: Minimum length of 3\. Maximum length of 255\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: No

 ** KeyConditionExpression **   
The condition that specifies the key value\(s\) for items to be retrieved by the `Query` action\.  
The condition must perform an equality test on a single partition key value\.  
The condition can optionally perform one of several comparison tests on a single sort key value\. This allows `Query` to retrieve one item with a given partition key value and sort key value, or several items that have the same partition key value but different sort key values\.  
The partition key equality test is required, and must be specified in the following format:  
 `partitionKeyName` *=* `:partitionkeyval`   
If you also want to provide a condition for the sort key, it must be combined using `AND` with the condition for the sort key\. Following is an example, using the **=** comparison operator for the sort key:  
 `partitionKeyName` `=` `:partitionkeyval` `AND` `sortKeyName` `=` `:sortkeyval`   
Valid comparisons for the sort key condition are as follows:  

+  `sortKeyName` `=` `:sortkeyval` \- true if the sort key value is equal to `:sortkeyval`\.

+  `sortKeyName` `<` `:sortkeyval` \- true if the sort key value is less than `:sortkeyval`\.

+  `sortKeyName` `<=` `:sortkeyval` \- true if the sort key value is less than or equal to `:sortkeyval`\.

+  `sortKeyName` `>` `:sortkeyval` \- true if the sort key value is greater than `:sortkeyval`\.

+  `sortKeyName` `>= ` `:sortkeyval` \- true if the sort key value is greater than or equal to `:sortkeyval`\.

+  `sortKeyName` `BETWEEN` `:sortkeyval1` `AND` `:sortkeyval2` \- true if the sort key value is greater than or equal to `:sortkeyval1`, and less than or equal to `:sortkeyval2`\.

+  `begins_with (` `sortKeyName`, `:sortkeyval` `)` \- true if the sort key value begins with a particular operand\. \(You cannot use this function with a sort key that is of type Number\.\) Note that the function name `begins_with` is case\-sensitive\.
Use the `ExpressionAttributeValues` parameter to replace tokens such as `:partitionval` and `:sortval` with actual values at runtime\.  
You can optionally use the `ExpressionAttributeNames` parameter to replace the names of the partition key and sort key with placeholder tokens\. This option might be necessary if an attribute name conflicts with a DynamoDB reserved word\. For example, the following `KeyConditionExpression` parameter causes an error because *Size* is a reserved word:  

+  `Size = :myval` 
To work around this, define a placeholder \(such a `#S`\) to represent the attribute name *Size*\. `KeyConditionExpression` then is as follows:  

+  `#S = :myval` 
For a list of reserved words, see [Reserved Words](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ReservedWords.html) in the *Amazon DynamoDB Developer Guide*\.  
For more information on `ExpressionAttributeNames` and `ExpressionAttributeValues`, see [Using Placeholders for Attribute Names and Values](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ExpressionPlaceholders.html) in the *Amazon DynamoDB Developer Guide*\.  
Type: String  
Required: No

 ** KeyConditions **   
This is a legacy parameter\. Use `KeyConditionExpression` instead\. For more information, see [KeyConditions](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/LegacyConditionalParameters.KeyConditions.html) in the *Amazon DynamoDB Developer Guide*\.  
Type: String to [Condition](API_Condition.md) object map  
Key Length Constraints: Maximum length of 65535\.  
Required: No

 ** Limit **   
The maximum number of items to evaluate \(not necessarily the number of matching items\)\. If DynamoDB processes the number of items up to the limit while processing the results, it stops the operation and returns the matching values up to that point, and a key in `LastEvaluatedKey` to apply in a subsequent operation, so that you can pick up where you left off\. Also, if the processed data set size exceeds 1 MB before DynamoDB reaches this limit, it stops the operation and returns the matching values up to the limit, and a key in `LastEvaluatedKey` to apply in a subsequent operation to continue the operation\. For more information, see [Query and Scan](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/QueryAndScan.html) in the *Amazon DynamoDB Developer Guide*\.  
Type: Integer  
Valid Range: Minimum value of 1\.  
Required: No

 ** ProjectionExpression **   
A string that identifies one or more attributes to retrieve from the table\. These attributes can include scalars, sets, or elements of a JSON document\. The attributes in the expression must be separated by commas\.  
If no attribute names are specified, then all attributes will be returned\. If any of the requested attributes are not found, they will not appear in the result\.  
For more information, see [Accessing Item Attributes](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.AccessingItemAttributes.html) in the *Amazon DynamoDB Developer Guide*\.  
Type: String  
Required: No

 ** QueryFilter **   
This is a legacy parameter\. Use `FilterExpression` instead\. For more information, see [QueryFilter](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/LegacyConditionalParameters.QueryFilter.html) in the *Amazon DynamoDB Developer Guide*\.  
Type: String to [Condition](API_Condition.md) object map  
Key Length Constraints: Maximum length of 65535\.  
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

 ** ScanIndexForward **   
Specifies the order for index traversal: If `true` \(default\), the traversal is performed in ascending order; if `false`, the traversal is performed in descending order\.   
Items with the same partition key value are stored in sorted order by sort key\. If the sort key data type is Number, the results are stored in numeric order\. For type String, the results are stored in order of ASCII character code values\. For type Binary, DynamoDB treats each byte of the binary data as unsigned\.  
If `ScanIndexForward` is `true`, DynamoDB returns the results in the order in which they are stored \(by sort key value\)\. This is the default behavior\. If `ScanIndexForward` is `false`, DynamoDB reads the results in reverse order by sort key value, and then returns the results to the client\.  
Type: Boolean  
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

## Response Syntax<a name="API_Query_ResponseSyntax"></a>

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

## Response Elements<a name="API_Query_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** ConsumedCapacity **   
The capacity units consumed by the `Query` operation\. The data returned includes the total provisioned throughput consumed, along with statistics for the table and any indexes involved in the operation\. `ConsumedCapacity` is only returned if the `ReturnConsumedCapacity` parameter was specified For more information, see [Provisioned Throughput](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ProvisionedThroughputIntro.html) in the *Amazon DynamoDB Developer Guide*\.  
Type: [ConsumedCapacity](API_ConsumedCapacity.md) object

 ** Count **   
The number of items in the response\.  
If you used a `QueryFilter` in the request, then `Count` is the number of items returned after the filter was applied, and `ScannedCount` is the number of matching items before the filter was applied\.  
If you did not use a filter in the request, then `Count` and `ScannedCount` are the same\.  
Type: Integer

 ** Items **   
An array of item attributes that match the query criteria\. Each element in this array consists of an attribute name and the value for that attribute\.  
Type: Array of string to [AttributeValue](API_AttributeValue.md) object maps  
Key Length Constraints: Maximum length of 65535\.

 ** LastEvaluatedKey **   
The primary key of the item where the operation stopped, inclusive of the previous result set\. Use this value to start a new operation, excluding this value in the new request\.  
If `LastEvaluatedKey` is empty, then the "last page" of results has been processed and there is no more data to be retrieved\.  
If `LastEvaluatedKey` is not empty, it does not necessarily mean that there is more data in the result set\. The only way to know when you have reached the end of the result set is when `LastEvaluatedKey` is empty\.  
Type: String to [AttributeValue](API_AttributeValue.md) object map  
Key Length Constraints: Maximum length of 65535\.

 ** ScannedCount **   
The number of items evaluated, before any `QueryFilter` is applied\. A high `ScannedCount` value with few, or no, `Count` results indicates an inefficient `Query` operation\. For more information, see [Count and ScannedCount](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/QueryAndScan.html#Count) in the *Amazon DynamoDB Developer Guide*\.  
If you did not use a filter in the request, then `ScannedCount` is the same as `Count`\.  
Type: Integer

## Errors<a name="API_Query_Errors"></a>

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

## Examples<a name="API_Query_Examples"></a>

### Retrieve a Range of Items<a name="API_Query_Example_1"></a>

The following example queries the *Reply* table for replies in a forum that were posted by particular users\. There is a local secondary index on the *Reply* table, `PostedBy-Index`, to facilitate fast lookups on the these attributes\.

The `ProjectionExpression` parameter determines which attributes are returned\.

#### Sample Request<a name="API_Query_Example_1_Request"></a>

```
POST / HTTP/1.1
Host: dynamodb.<region>.<domain>;
Accept-Encoding: identity
Content-Length: <PayloadSizeBytes>     
User-Agent: <UserAgentString>
Content-Type: application/x-amz-json-1.0
Authorization: AWS4-HMAC-SHA256 Credential=<Credential>, SignedHeaders=<Headers>, Signature=<Signature>
X-Amz-Date: <Date> 
X-Amz-Target: DynamoDB_20120810.Query 

{
    "TableName": "Reply",
    "IndexName": "PostedBy-Index",
    "Limit": 3,
    "ConsistentRead": true,
    "ProjectionExpression": "Id, PostedBy, ReplyDateTime",
    "KeyConditionExpression": "Id = :v1 AND PostedBy BETWEEN :v2a AND :v2b",
    "ExpressionAttributeValues": {
        ":v1": {"S": "Amazon DynamoDB#DynamoDB Thread 1"},
        ":v2a": {"S": "User A"},
        ":v2b": {"S": "User C"}
    },
    "ReturnConsumedCapacity": "TOTAL"
}
```

#### Sample Response<a name="API_Query_Example_1_Response"></a>

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
        "TableName": "Reply"
    },
    "Count": 2,
    "Items": [
        {
            "ReplyDateTime": {"S": "2015-02-18T20:27:36.165Z"},
            "PostedBy": {"S": "User A"},
            "Id": {"S": "Amazon DynamoDB#DynamoDB Thread 1"}
        },
        {
            "ReplyDateTime": {"S": "2015-02-25T20:27:36.165Z"},
            "PostedBy": {"S": "User B"},
            "Id": {"S": "Amazon DynamoDB#DynamoDB Thread 1"}
        }
    ],
    "ScannedCount": 2
}
```

### Count Items<a name="API_Query_Example_2"></a>

The following example returns the number of items in the Thread table for a particular forum\.

#### Sample Request<a name="API_Query_Example_2_Request"></a>

```
POST / HTTP/1.1
Host: dynamodb.<region>.<domain>;
Accept-Encoding: identity
Content-Length: <PayloadSizeBytes>     
User-Agent: <UserAgentString>
Content-Type: application/x-amz-json-1.0
Authorization: AWS4-HMAC-SHA256 Credential=<Credential>, SignedHeaders=<Headers>, Signature=<Signature>
X-Amz-Date: <Date> 
X-Amz-Target: DynamoDB_20120810.Query 

{
    "TableName": "Thread",
    "ConsistentRead": true,
    "KeyConditionExpression": "ForumName = :val",
    "ExpressionAttributeValues": {":val": {"S": "Amazon DynamoDB"}}
}
```

#### Sample Response<a name="API_Query_Example_2_Response"></a>

```
HTTP/1.1 200 OK
x-amzn-RequestId: <RequestId> 
x-amz-crc32: <Checksum>
Content-Type: application/x-amz-json-1.0
Content-Length: <PayloadSizeBytes>
Date: <Date>
 {
          "Count":2,
          "ScannedCount":2
}
```

## See Also<a name="API_Query_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/dynamodb-2012-08-10/Query) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/dynamodb-2012-08-10/Query) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/Query) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/Query) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/Query) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/dynamodb-2012-08-10/Query) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/dynamodb-2012-08-10/Query) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/dynamodb-2012-08-10/Query) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/Query) 