# BatchWriteItem<a name="API_BatchWriteItem"></a>

The `BatchWriteItem` operation puts or deletes multiple items in one or more tables\. A single call to `BatchWriteItem` can write up to 16 MB of data, which can comprise as many as 25 put or delete requests\. Individual items to be written can be as large as 400 KB\.

**Note**  
 `BatchWriteItem` cannot update items\. To update items, use the `UpdateItem` action\.

The individual `PutItem` and `DeleteItem` operations specified in `BatchWriteItem` are atomic; however `BatchWriteItem` as a whole is not\. If any requested operations fail because the table's provisioned throughput is exceeded or an internal processing failure occurs, the failed operations are returned in the `UnprocessedItems` response parameter\. You can investigate and optionally resend the requests\. Typically, you would call `BatchWriteItem` in a loop\. Each iteration would check for unprocessed items and submit a new `BatchWriteItem` request with those unprocessed items until all items have been processed\.

Note that if *none* of the items can be processed due to insufficient provisioned throughput on all of the tables in the request, then `BatchWriteItem` will return a `ProvisionedThroughputExceededException`\.

**Important**  
If DynamoDB returns any unprocessed items, you should retry the batch operation on those items\. However, *we strongly recommend that you use an exponential backoff algorithm*\. If you retry the batch operation immediately, the underlying read or write requests can still fail due to throttling on the individual tables\. If you delay the batch operation using exponential backoff, the individual requests in the batch are much more likely to succeed\.  
For more information, see [Batch Operations and Error Handling](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ErrorHandling.html#BatchOperations) in the *Amazon DynamoDB Developer Guide*\.

With `BatchWriteItem`, you can efficiently write or delete large amounts of data, such as from Amazon Elastic MapReduce \(EMR\), or copy data from another database into DynamoDB\. In order to improve performance with these large\-scale operations, `BatchWriteItem` does not behave in the same way as individual `PutItem` and `DeleteItem` calls would\. For example, you cannot specify conditions on individual put and delete requests, and `BatchWriteItem` does not return deleted items in the response\.

If you use a programming language that supports concurrency, you can use threads to write items in parallel\. Your application must include the necessary logic to manage the threads\. With languages that don't support threading, you must update or delete the specified items one at a time\. In both situations, `BatchWriteItem` performs the specified put and delete operations in parallel, giving you the power of the thread pool approach without having to introduce complexity into your application\.

Parallel processing reduces latency, but each specified put and delete request consumes the same number of write capacity units whether it is processed in parallel or not\. Delete operations on nonexistent items consume one write capacity unit\.

If one or more of the following is true, DynamoDB rejects the entire batch write operation:

+ One or more tables specified in the `BatchWriteItem` request does not exist\.

+ Primary key attributes specified on an item in the request do not match those in the corresponding table's primary key schema\.

+ You try to perform multiple operations on the same item in the same `BatchWriteItem` request\. For example, you cannot put and delete the same item in the same `BatchWriteItem` request\. 

+ There are more than 25 requests in the batch\.

+ Any individual item in a batch exceeds 400 KB\.

+ The total request size exceeds 16 MB\.

## Request Syntax<a name="API_BatchWriteItem_RequestSyntax"></a>

```
{
   "RequestItems": { 
      "string" : [ 
         { 
            "DeleteRequest": { 
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
               }
            },
            "PutRequest": { 
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
         }
      ]
   },
   "ReturnConsumedCapacity": "string",
   "ReturnItemCollectionMetrics": "string"
}
```

## Request Parameters<a name="API_BatchWriteItem_RequestParameters"></a>

The request accepts the following data in JSON format\.

**Note**  
In the following list, the required parameters are described first\.

 ** RequestItems **   
A map of one or more table names and, for each table, a list of operations to be performed \(`DeleteRequest` or `PutRequest`\)\. Each element in the map consists of the following:  

+  `DeleteRequest` \- Perform a `DeleteItem` operation on the specified item\. The item to be deleted is identified by a `Key` subelement:

  +  `Key` \- A map of primary key attribute values that uniquely identify the item\. Each entry in this map consists of an attribute name and an attribute value\. For each primary key, you must provide *all* of the key attributes\. For example, with a simple primary key, you only need to provide a value for the partition key\. For a composite primary key, you must provide values for *both* the partition key and the sort key\.

+  `PutRequest` \- Perform a `PutItem` operation on the specified item\. The item to be put is identified by an `Item` subelement:

  +  `Item` \- A map of attributes and their values\. Each entry in this map consists of an attribute name and an attribute value\. Attribute values must not be null; string and binary type attributes must have lengths greater than zero; and set type attributes must not be empty\. Requests that contain empty values will be rejected with a `ValidationException` exception\.

    If you specify any attributes that are part of an index key, then the data types for those attributes must match those of the schema in the table's attribute definition\.
Type: String to array of [WriteRequest](API_WriteRequest.md) objects map  
Key Length Constraints: Minimum length of 3\. Maximum length of 255\.  
Key Pattern: `[a-zA-Z0-9_.-]+`   
Array Members: Minimum number of 1 item\. Maximum number of 25 items\.  
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

 ** ReturnItemCollectionMetrics **   
Determines whether item collection metrics are returned\. If set to `SIZE`, the response includes statistics about item collections, if any, that were modified during the operation are returned in the response\. If set to `NONE` \(the default\), no statistics are returned\.  
Type: String  
Valid Values:` SIZE | NONE`   
Required: No

## Response Syntax<a name="API_BatchWriteItem_ResponseSyntax"></a>

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
   "ItemCollectionMetrics": { 
      "string" : [ 
         { 
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
      ]
   },
   "UnprocessedItems": { 
      "string" : [ 
         { 
            "DeleteRequest": { 
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
               }
            },
            "PutRequest": { 
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
         }
      ]
   }
}
```

## Response Elements<a name="API_BatchWriteItem_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** ConsumedCapacity **   
The capacity units consumed by the entire `BatchWriteItem` operation\.  
Each element consists of:  

+  `TableName` \- The table that consumed the provisioned throughput\.

+  `CapacityUnits` \- The total number of capacity units consumed\.
Type: Array of [ConsumedCapacity](API_ConsumedCapacity.md) objects

 ** ItemCollectionMetrics **   
A list of tables that were processed by `BatchWriteItem` and, for each table, information about any item collections that were affected by individual `DeleteItem` or `PutItem` operations\.  
Each entry consists of the following subelements:  

+  `ItemCollectionKey` \- The partition key value of the item collection\. This is the same as the partition key value of the item\.

+  `SizeEstimateRangeGB` \- An estimate of item collection size, expressed in GB\. This is a two\-element array containing a lower bound and an upper bound for the estimate\. The estimate includes the size of all the items in the table, plus the size of all attributes projected into all of the local secondary indexes on the table\. Use this estimate to measure whether a local secondary index is approaching its size limit\.

  The estimate is subject to change over time; therefore, do not rely on the precision or accuracy of the estimate\.
Type: String to array of [ItemCollectionMetrics](API_ItemCollectionMetrics.md) objects map  
Key Length Constraints: Minimum length of 3\. Maximum length of 255\.  
Key Pattern: `[a-zA-Z0-9_.-]+` 

 ** UnprocessedItems **   
A map of tables and requests against those tables that were not processed\. The `UnprocessedItems` value is in the same form as `RequestItems`, so you can provide this value directly to a subsequent `BatchGetItem` operation\. For more information, see `RequestItems` in the Request Parameters section\.  
Each `UnprocessedItems` entry consists of a table name and, for that table, a list of operations to perform \(`DeleteRequest` or `PutRequest`\)\.  

+  `DeleteRequest` \- Perform a `DeleteItem` operation on the specified item\. The item to be deleted is identified by a `Key` subelement:

  +  `Key` \- A map of primary key attribute values that uniquely identify the item\. Each entry in this map consists of an attribute name and an attribute value\.

+  `PutRequest` \- Perform a `PutItem` operation on the specified item\. The item to be put is identified by an `Item` subelement:

  +  `Item` \- A map of attributes and their values\. Each entry in this map consists of an attribute name and an attribute value\. Attribute values must not be null; string and binary type attributes must have lengths greater than zero; and set type attributes must not be empty\. Requests that contain empty values will be rejected with a `ValidationException` exception\.

    If you specify any attributes that are part of an index key, then the data types for those attributes must match those of the schema in the table's attribute definition\.
If there are no unprocessed items remaining, the response contains an empty `UnprocessedItems` map\.  
Type: String to array of [WriteRequest](API_WriteRequest.md) objects map  
Key Length Constraints: Minimum length of 3\. Maximum length of 255\.  
Key Pattern: `[a-zA-Z0-9_.-]+`   
Array Members: Minimum number of 1 item\. Maximum number of 25 items\.

## Errors<a name="API_BatchWriteItem_Errors"></a>

For information about the errors that are common to all actions, see [Common Errors](CommonErrors.md)\.

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

## Example<a name="API_BatchWriteItem_Examples"></a>

### Multiple Operations on One Table<a name="API_BatchWriteItem_Example_1"></a>

This example writes several items to the Forum table\. The response shows that the final put operation failed, possibly because the application exceeded the provisioned throughput on the table\. The UnprocessedItems object shows the unsuccessful put request\. The application can call BatchWriteItem again to address such unprocessed requests\.

#### Sample Request<a name="API_BatchWriteItem_Example_1_Request"></a>

```
POST / HTTP/1.1
Host: dynamodb.<region>.<domain>;
Accept-Encoding: identity
Content-Length: <PayloadSizeBytes>     
User-Agent: <UserAgentString>
Content-Type: application/x-amz-json-1.0
Authorization: AWS4-HMAC-SHA256 Credential=<Credential>, SignedHeaders=<Headers>, Signature=<Signature>
X-Amz-Date: <Date> 
X-Amz-Target: DynamoDB_20120810.BatchWriteItem 

{
    "RequestItems": {
        "Forum": [
            {
                "PutRequest": {
                    "Item": {
                        "Name": {
                            "S": "Amazon DynamoDB"
                        },
                        "Category": {
                            "S": "Amazon Web Services"
                        }
                    }
                }
            },
            {
                "PutRequest": {
                    "Item": {
                        "Name": {
                            "S": "Amazon RDS"
                        },
                        "Category": {
                            "S": "Amazon Web Services"
                        }
                    }
                }
            },
            {
                "PutRequest": {
                    "Item": {
                        "Name": {
                            "S": "Amazon Redshift"
                        },
                        "Category": {
                            "S": "Amazon Web Services"
                        }
                    }
                }
            },
            {
                "PutRequest": {
                    "Item": {
                        "Name": {
                            "S": "Amazon ElastiCache"
                        },
                        "Category": {
                            "S": "Amazon Web Services"
                        }
                    }
                }
            }
        ]
    },
    "ReturnConsumedCapacity": "TOTAL"
}
```

#### Sample Response<a name="API_BatchWriteItem_Example_1_Response"></a>

```
HTTP/1.1 200 OK
x-amzn-RequestId: <RequestId> 
x-amz-crc32: <Checksum>
Content-Type: application/x-amz-json-1.0
Content-Length: <PayloadSizeBytes>
Date: <Date>
 {
    "UnprocessedItems": {
        "Forum": [
            {
                "PutRequest": {
                    "Item": {
                        "Name": {
                            "S": "Amazon ElastiCache"
                        },
                        "Category": {
                            "S": "Amazon Web Services"
                        }
                    }
                }
            }
        ]
    },
    "ConsumedCapacity": [
        {
            "TableName": "Forum",
            "CapacityUnits": 3
        }
    ]
}
```

## See Also<a name="API_BatchWriteItem_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/dynamodb-2012-08-10/BatchWriteItem) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/dynamodb-2012-08-10/BatchWriteItem) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/BatchWriteItem) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/BatchWriteItem) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/BatchWriteItem) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/dynamodb-2012-08-10/BatchWriteItem) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/dynamodb-2012-08-10/BatchWriteItem) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/dynamodb-2012-08-10/BatchWriteItem) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/BatchWriteItem) 