# BatchWriteItem<a name="API_BatchWriteItem_v20111205"></a>

**Important**  
***This section refers to API version 2011\-12\-05, which is deprecated and should not be used for new applications\.***  
 **For documentation on the current low\-level API, see the [Amazon DynamoDB API Reference](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/)\.**

## Description<a name="API_BatchWriteItems_Description"></a>

This operation enables you to put or delete several items across multiple tables in a single call\. 

To upload one item, you can use `PutItem`, and to delete one item, you can use `DeleteItem`\. However, when you want to upload or delete large amounts of data, such as uploading large amounts of data from Amazon EMR \(Amazon EMR\) or migrating data from another database in to DynamoDB, `BatchWriteItem` offers an efficient alternative\. 

If you use languages such as Java, you can use threads to upload items in parallel\. This adds complexity in your application to handle the threads\. Other languages don't support threading\. For example, if you are using PHP, you must upload or delete items one at a time\. In both situations, `BatchWriteItem` provides an alternative where the specified put and delete operations are processed in parallel, giving you the power of the thread pool approach without having to introduce complexity in your application\. 

Note that each individual put and delete specified in a `BatchWriteItem` operation costs the same in terms of consumed capacity units\. However, because `BatchWriteItem` performs the specified operations in parallel, you get lower latency\. Delete operations on non\-existent items consume 1 write capacity unit\. For more information about consumed capacity units, see [Working with tables and data in DynamoDB](WorkingWithTables.md)\.

When using `BatchWriteItem`, note the following limitations:
+ **Maximum operations in a single request—**You can specify a total of up to 25 put or delete operations; however, the total request size cannot exceed 1 MB \(the HTTP payload\)\. 
+ You can use the `BatchWriteItem` operation only to put and delete items\. You cannot use it to update existing items\.
+ **Not an atomic operation—**Individual operations specified in a `BatchWriteItem` are atomic; however `BatchWriteItem` as a whole is a "best\-effort" operation and not an atomic operation\. That is, in a `BatchWriteItem` request, some operations might succeed and others might fail\. The failed operations are returned in an `UnprocessedItems` field in the response\. Some of these failures might be because you exceeded the provisioned throughput configured for the table or a transient failure such as a network error\. You can investigate and optionally resend the requests\. Typically, you call `BatchWriteItem` in a loop and in each iteration check for unprocessed items, and submit a new `BatchWriteItem` request with those unprocessed items\. 
+ **Does not return any items—**The `BatchWriteItem` is designed for uploading large amounts of data efficiently\. It does not provide some of the sophistication offered by `PutItem` and `DeleteItem`\. For example, `DeleteItem` supports the `ReturnValues` field in your request body to request the deleted item in the response\. The `BatchWriteItem` operation does not return any items in the response\.
+ Unlike `PutItem` and `DeleteItem`, `BatchWriteItem` does not allow you to specify conditions on individual write requests in the operation\.
+ Attribute values must not be null; string and binary type attributes must have lengths greater than zero; and set type attributes must not be empty\. Requests that have empty values will be rejected with a `ValidationException`\.

DynamoDB rejects the entire batch write operation if any one of the following is true: 
+ If one or more tables specified in the `BatchWriteItem` request does not exist\.
+ If primary key attributes specified on an item in the request does not match the corresponding table's primary key schema\.
+ If you try to perform multiple operations on the same item in the same `BatchWriteItem` request\. For example, you cannot put and delete the same item in the same `BatchWriteItem` request\. 
+ If the total request size exceeds the 1 MB request size \(the HTTP payload\) limit\.
+ If any individual item in a batch exceeds the 64 KB item size limit\.

## Requests<a name="API_BatchWriteItems_RequestParameters"></a>

### Syntax<a name="API_BatchWriteItems_RequestParameters.syntax"></a>

```
// This header is abbreviated. For a sample of a complete header, see DynamoDB low\-level API.
POST / HTTP/1.1
x-amz-target: DynamoDB_20111205.BatchGetItem 
content-type: application/x-amz-json-1.0 

{
  "RequestItems" :  RequestItems
} 

RequestItems
{
     "TableName1" :  [ Request, Request, ... ],
     "TableName2" :  [ Request, Request, ... ],
     ...
}

Request ::=
  PutRequest | DeleteRequest

PutRequest ::=
{
  "PutRequest" : {
     "Item" : {
        "Attribute-Name1" : Attribute-Value,
        "Attribute-Name2" : Attribute-Value,
         ...
     }
  }
}

DeleteRequest ::=
{
   "DeleteRequest" : {
      "Key" : PrimaryKey-Value
   }
}


PrimaryKey-Value ::= HashTypePK | HashAndRangeTypePK

HashTypePK ::=
{ 
   "HashKeyElement" : Attribute-Value
}

HashAndRangeTypePK
{ 
   "HashKeyElement" : Attribute-Value,
   "RangeKeyElement" : Attribute-Value, 
}

Attribute-Value ::= String | Numeric| Binary | StringSet | NumericSet | BinarySet 

Numeric ::=
{
   "N": "Number"
}

String ::=
{
   "S": "String"
}

Binary ::=
{
    "B": "Base64 encoded binary data"
}

StringSet ::=
{
   "SS": [ "String1", "String2", ... ]
}

NumberSet ::=
{
   "NS": [ "Number1", "Number2", ... ]
}  

BinarySet ::=
{
   "BS": [ "Binary1", "Binary2", ... ]
}
```

In the request body, the `RequestItems` JSON object describes the operations that you want to perform\. The operations are grouped by tables\. You can use `BatchWriteItem` to update or delete several items across multiple tables\. For each specific write request, you must identify the type of request \(`PutItem`, `DeleteItem`\) followed by detail information about the operation\. 
+ For a `PutRequest`, you provide the item, that is, a list of attributes and their values\.
+ For a `DeleteRequest`, you provide the primary key name and value\. 

## Responses<a name="API_BatchWriteItems_ResponseElements"></a>

### Syntax<a name="API_BatchWriteItems_ResponseElements.syntax"></a>

The following is the syntax of the JSON body returned in the response\.

```
{
  "Responses" :         ConsumedCapacityUnitsByTable
  "UnprocessedItems" :  RequestItems
} 

ConsumedCapacityUnitsByTable
{
    "TableName1" : { "ConsumedCapacityUnits", : NumericValue },
    "TableName2" : { "ConsumedCapacityUnits", : NumericValue },
     ...
}

RequestItems
This syntax is identical to the one described in the JSON syntax in the request.
```

## Special errors<a name="API_BatchWriteItems_SpecialErrors"></a>

No errors specific to this operation\.

## Examples<a name="API_BatchWriteItems_Examples"></a>

The following example shows an HTTP POST request and the response of a `BatchWriteItem` operation\. The request specifies the following operations on the Reply and the Thread tables:
+ Put an item and delete an item from the Reply table
+ Put an item into the Thread table

For examples using the AWS SDK, see [Working with items and attributes](WorkingWithItems.md)\.

### Sample request<a name="API_BatchWriteItems_Examples_Request"></a>

```
// This header is abbreviated. For a sample of a complete header, see DynamoDB low\-level API.
POST / HTTP/1.1
x-amz-target: DynamoDB_20111205.BatchGetItem 
content-type: application/x-amz-json-1.0 

{
  "RequestItems":{
    "Reply":[
      {
        "PutRequest":{
          "Item":{
            "ReplyDateTime":{
              "S":"2012-04-03T11:04:47.034Z"
            },
            "Id":{
              "S":"DynamoDB#DynamoDB Thread 5"
            }
          }
        }
      },
      {
        "DeleteRequest":{
          "Key":{
            "HashKeyElement":{
              "S":"DynamoDB#DynamoDB Thread 4"
            },
            "RangeKeyElement":{
              "S":"oops - accidental row"
            }
          }
        }
      }
    ],
    "Thread":[
      {
        "PutRequest":{
          "Item":{
            "ForumName":{
              "S":"DynamoDB"
            },
            "Subject":{
              "S":"DynamoDB Thread 5"
            }
          }
        }
      }
    ]
  }
}
```

### Sample response<a name="API_BatchWriteItems_Examples_Response"></a>

The following example response shows a put operation on both the Thread and Reply tables succeeded and a delete operation on the Reply table failed \(for reasons such as throttling that is caused when you exceed the provisioned throughput on the table\)\. Note the following in the JSON response:
+ The `Responses` object shows one capacity unit was consumed on both the `Thread` and `Reply` tables as a result of the successful put operation on each of these tables\. 
+ The `UnprocessedItems` object shows the unsuccessful delete operation on the `Reply` table\. You can then issue a new `BatchWriteItem` call to address these unprocessed requests\.

```
HTTP/1.1 200 OK
x-amzn-RequestId: G8M9ANLOE5QA26AEUHJKJE0ASBVV4KQNSO5AEMVJF66Q9ASUAAJG
Content-Type: application/x-amz-json-1.0
Content-Length: 536
Date: Thu, 05 Apr 2012 18:22:09 GMT

{
   "Responses":{
      "Thread":{
         "ConsumedCapacityUnits":1.0
      },
      "Reply":{
         "ConsumedCapacityUnits":1.0
      }
   },
   "UnprocessedItems":{
      "Reply":[
         {
            "DeleteRequest":{
               "Key":{
                  "HashKeyElement":{
                     "S":"DynamoDB#DynamoDB Thread 4"
                  },
                  "RangeKeyElement":{
                     "S":"oops - accidental row"
                  }
               }
            }
         }
      ]
   }
}
```