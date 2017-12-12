# CreateTable<a name="API_CreateTable"></a>

The `CreateTable` operation adds a new table to your account\. In an AWS account, table names must be unique within each region\. That is, you can have two tables with same name if you create the tables in different regions\.

 `CreateTable` is an asynchronous operation\. Upon receiving a `CreateTable` request, DynamoDB immediately returns a response with a `TableStatus` of `CREATING`\. After the table is created, DynamoDB sets the `TableStatus` to `ACTIVE`\. You can perform read and write operations only on an `ACTIVE` table\. 

You can optionally define secondary indexes on the new table, as part of the `CreateTable` operation\. If you want to create multiple tables with secondary indexes on them, you must create the tables sequentially\. Only one table with secondary indexes can be in the `CREATING` state at any given time\.

You can use the `DescribeTable` action to check the table status\.

## Request Syntax<a name="API_CreateTable_RequestSyntax"></a>

```
{
   "AttributeDefinitions": [ 
      { 
         "AttributeName": "string",
         "AttributeType": "string"
      }
   ],
   "GlobalSecondaryIndexes": [ 
      { 
         "IndexName": "string",
         "KeySchema": [ 
            { 
               "AttributeName": "string",
               "KeyType": "string"
            }
         ],
         "Projection": { 
            "NonKeyAttributes": [ "string" ],
            "ProjectionType": "string"
         },
         "ProvisionedThroughput": { 
            "ReadCapacityUnits": number,
            "WriteCapacityUnits": number
         }
      }
   ],
   "KeySchema": [ 
      { 
         "AttributeName": "string",
         "KeyType": "string"
      }
   ],
   "LocalSecondaryIndexes": [ 
      { 
         "IndexName": "string",
         "KeySchema": [ 
            { 
               "AttributeName": "string",
               "KeyType": "string"
            }
         ],
         "Projection": { 
            "NonKeyAttributes": [ "string" ],
            "ProjectionType": "string"
         }
      }
   ],
   "ProvisionedThroughput": { 
      "ReadCapacityUnits": number,
      "WriteCapacityUnits": number
   },
   "StreamSpecification": { 
      "StreamEnabled": boolean,
      "StreamViewType": "string"
   },
   "TableName": "string"
}
```

## Request Parameters<a name="API_CreateTable_RequestParameters"></a>

The request accepts the following data in JSON format\.

**Note**  
In the following list, the required parameters are described first\.

 ** AttributeDefinitions **   
An array of attributes that describe the key schema for the table and indexes\.  
Type: Array of [AttributeDefinition](API_AttributeDefinition.md) objects  
Required: Yes

 ** KeySchema **   
Specifies the attributes that make up the primary key for a table or an index\. The attributes in `KeySchema` must also be defined in the `AttributeDefinitions` array\. For more information, see [Data Model](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DataModel.html) in the *Amazon DynamoDB Developer Guide*\.  
Each `KeySchemaElement` in the array is composed of:  

+  `AttributeName` \- The name of this key attribute\.

+  `KeyType` \- The role that the key attribute will assume:

  +  `HASH` \- partition key

  +  `RANGE` \- sort key
The partition key of an item is also known as its *hash attribute*\. The term "hash attribute" derives from DynamoDB' usage of an internal hash function to evenly distribute data items across partitions, based on their partition key values\.  
The sort key of an item is also known as its *range attribute*\. The term "range attribute" derives from the way DynamoDB stores items with the same partition key physically close together, in sorted order by the sort key value\.
For a simple primary key \(partition key\), you must provide exactly one element with a `KeyType` of `HASH`\.  
For a composite primary key \(partition key and sort key\), you must provide exactly two elements, in this order: The first element must have a `KeyType` of `HASH`, and the second element must have a `KeyType` of `RANGE`\.  
For more information, see [Specifying the Primary Key](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/WorkingWithTables.html#WorkingWithTables.primary.key) in the *Amazon DynamoDB Developer Guide*\.  
Type: Array of [KeySchemaElement](API_KeySchemaElement.md) objects  
Array Members: Minimum number of 1 item\. Maximum number of 2 items\.  
Required: Yes

 ** ProvisionedThroughput **   
Represents the provisioned throughput settings for a specified table or index\. The settings can be modified using the `UpdateTable` operation\.  
For current minimum and maximum provisioned throughput values, see [Limits](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Limits.html) in the *Amazon DynamoDB Developer Guide*\.  
Type: [ProvisionedThroughput](API_ProvisionedThroughput.md) object  
Required: Yes

 ** TableName **   
The name of the table to create\.  
Type: String  
Length Constraints: Minimum length of 3\. Maximum length of 255\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

 ** GlobalSecondaryIndexes **   
One or more global secondary indexes \(the maximum is five\) to be created on the table\. Each global secondary index in the array includes the following:  

+  `IndexName` \- The name of the global secondary index\. Must be unique only for this table\.

  

+  `KeySchema` \- Specifies the key schema for the global secondary index\.

+  `Projection` \- Specifies attributes that are copied \(projected\) from the table into the index\. These are in addition to the primary key attributes and index key attributes, which are automatically projected\. Each attribute specification is composed of:

  +  `ProjectionType` \- One of the following:

    +  `KEYS_ONLY` \- Only the index and primary keys are projected into the index\.

    +  `INCLUDE` \- Only the specified table attributes are projected into the index\. The list of projected attributes are in `NonKeyAttributes`\.

    +  `ALL` \- All of the table attributes are projected into the index\.

  +  `NonKeyAttributes` \- A list of one or more non\-key attribute names that are projected into the secondary index\. The total count of attributes provided in `NonKeyAttributes`, summed across all of the secondary indexes, must not exceed 20\. If you project the same attribute into two different indexes, this counts as two distinct attributes when determining the total\.

+  `ProvisionedThroughput` \- The provisioned throughput settings for the global secondary index, consisting of read and write capacity units\.
Type: Array of [GlobalSecondaryIndex](API_GlobalSecondaryIndex.md) objects  
Required: No

 ** LocalSecondaryIndexes **   
One or more local secondary indexes \(the maximum is five\) to be created on the table\. Each index is scoped to a given partition key value\. There is a 10 GB size limit per partition key value; otherwise, the size of a local secondary index is unconstrained\.  
Each local secondary index in the array includes the following:  

+  `IndexName` \- The name of the local secondary index\. Must be unique only for this table\.

  

+  `KeySchema` \- Specifies the key schema for the local secondary index\. The key schema must begin with the same partition key as the table\.

+  `Projection` \- Specifies attributes that are copied \(projected\) from the table into the index\. These are in addition to the primary key attributes and index key attributes, which are automatically projected\. Each attribute specification is composed of:

  +  `ProjectionType` \- One of the following:

    +  `KEYS_ONLY` \- Only the index and primary keys are projected into the index\.

    +  `INCLUDE` \- Only the specified table attributes are projected into the index\. The list of projected attributes are in `NonKeyAttributes`\.

    +  `ALL` \- All of the table attributes are projected into the index\.

  +  `NonKeyAttributes` \- A list of one or more non\-key attribute names that are projected into the secondary index\. The total count of attributes provided in `NonKeyAttributes`, summed across all of the secondary indexes, must not exceed 20\. If you project the same attribute into two different indexes, this counts as two distinct attributes when determining the total\.
Type: Array of [LocalSecondaryIndex](API_LocalSecondaryIndex.md) objects  
Required: No

 ** StreamSpecification **   
The settings for DynamoDB Streams on the table\. These settings consist of:  

+  `StreamEnabled` \- Indicates whether Streams is to be enabled \(true\) or disabled \(false\)\.

+  `StreamViewType` \- When an item in the table is modified, `StreamViewType` determines what information is written to the table's stream\. Valid values for `StreamViewType` are:

  +  `KEYS_ONLY` \- Only the key attributes of the modified item are written to the stream\.

  +  `NEW_IMAGE` \- The entire item, as it appears after it was modified, is written to the stream\.

  +  `OLD_IMAGE` \- The entire item, as it appeared before it was modified, is written to the stream\.

  +  `NEW_AND_OLD_IMAGES` \- Both the new and the old item images of the item are written to the stream\.
Type: [StreamSpecification](API_StreamSpecification.md) object  
Required: No

## Response Syntax<a name="API_CreateTable_ResponseSyntax"></a>

```
{
   "TableDescription": { 
      "AttributeDefinitions": [ 
         { 
            "AttributeName": "string",
            "AttributeType": "string"
         }
      ],
      "CreationDateTime": number,
      "GlobalSecondaryIndexes": [ 
         { 
            "Backfilling": boolean,
            "IndexArn": "string",
            "IndexName": "string",
            "IndexSizeBytes": number,
            "IndexStatus": "string",
            "ItemCount": number,
            "KeySchema": [ 
               { 
                  "AttributeName": "string",
                  "KeyType": "string"
               }
            ],
            "Projection": { 
               "NonKeyAttributes": [ "string" ],
               "ProjectionType": "string"
            },
            "ProvisionedThroughput": { 
               "LastDecreaseDateTime": number,
               "LastIncreaseDateTime": number,
               "NumberOfDecreasesToday": number,
               "ReadCapacityUnits": number,
               "WriteCapacityUnits": number
            }
         }
      ],
      "ItemCount": number,
      "KeySchema": [ 
         { 
            "AttributeName": "string",
            "KeyType": "string"
         }
      ],
      "LatestStreamArn": "string",
      "LatestStreamLabel": "string",
      "LocalSecondaryIndexes": [ 
         { 
            "IndexArn": "string",
            "IndexName": "string",
            "IndexSizeBytes": number,
            "ItemCount": number,
            "KeySchema": [ 
               { 
                  "AttributeName": "string",
                  "KeyType": "string"
               }
            ],
            "Projection": { 
               "NonKeyAttributes": [ "string" ],
               "ProjectionType": "string"
            }
         }
      ],
      "ProvisionedThroughput": { 
         "LastDecreaseDateTime": number,
         "LastIncreaseDateTime": number,
         "NumberOfDecreasesToday": number,
         "ReadCapacityUnits": number,
         "WriteCapacityUnits": number
      },
      "RestoreSummary": { 
         "RestoreDateTime": number,
         "RestoreInProgress": boolean,
         "SourceBackupArn": "string",
         "SourceTableArn": "string"
      },
      "StreamSpecification": { 
         "StreamEnabled": boolean,
         "StreamViewType": "string"
      },
      "TableArn": "string",
      "TableId": "string",
      "TableName": "string",
      "TableSizeBytes": number,
      "TableStatus": "string"
   }
}
```

## Response Elements<a name="API_CreateTable_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** TableDescription **   
Represents the properties of the table\.  
Type: [TableDescription](API_TableDescription.md) object

## Errors<a name="API_CreateTable_Errors"></a>

For information about the errors that are common to all actions, see [Common Errors](CommonErrors.md)\.

 **InternalServerError**   
An error occurred on the server side\.  
HTTP Status Code: 500

 **LimitExceededException**   
Up to 50 `CreateBackup` operations are allowed per second, per account\. There is no limit to the number of daily on\-demand backups that can be taken\.   
Up to 10 simultaneous table operations are allowed per account\. These operations include `CreateTable`, `UpdateTable`, `DeleteTable`,`UpdateTimeToLive`, and `RestoreTableFromBackup`\.   
For tables with secondary indexes, only one of those tables can be in the `CREATING` state at any point in time\. Do not attempt to create more than one such table simultaneously\.  
The total limit of tables in the `ACTIVE` state is 250\.  
For tables with secondary indexes, only one of those tables can be in the `CREATING` state at any point in time\. Do not attempt to create more than one such table simultaneously\.  
The total limit of tables in the `ACTIVE` state is 250\.  
HTTP Status Code: 400

 **ResourceInUseException**   
The operation conflicts with the resource's availability\. For example, you attempted to recreate an existing table, or tried to delete a table currently in the `CREATING` state\.  
HTTP Status Code: 400

## Example<a name="API_CreateTable_Examples"></a>

### Create a Table<a name="API_CreateTable_Example_1"></a>

This example creates a table named Thread\. The table primary key consists of ForumName \(partition key\) and Subject \(sort key\)\. A local secondary index is also created; its key consists of ForumName \(partition key\) and LastPostDateTime \(sort key\)\.

#### Sample Request<a name="API_CreateTable_Example_1_Request"></a>

```
POST / HTTP/1.1
Host: dynamodb.<region>.<domain>;
Accept-Encoding: identity
Content-Length: <PayloadSizeBytes>     
User-Agent: <UserAgentString>
Content-Type: application/x-amz-json-1.0
Authorization: AWS4-HMAC-SHA256 Credential=<Credential>, SignedHeaders=<Headers>, Signature=<Signature>
X-Amz-Date: <Date> 
X-Amz-Target: DynamoDB_20120810.CreateTable 

{
    "AttributeDefinitions": [
        {
            "AttributeName": "ForumName",
            "AttributeType": "S"
        },
        {
            "AttributeName": "Subject",
            "AttributeType": "S"
        },
        {
            "AttributeName": "LastPostDateTime",
            "AttributeType": "S"
        }
    ],
    "TableName": "Thread",
    "KeySchema": [
        {
            "AttributeName": "ForumName",
            "KeyType": "HASH"
        },
        {
            "AttributeName": "Subject",
            "KeyType": "RANGE"
        }
    ],
    "LocalSecondaryIndexes": [
        {
            "IndexName": "LastPostIndex",
            "KeySchema": [
                {
                    "AttributeName": "ForumName",
                    "KeyType": "HASH"
                },
                {
                    "AttributeName": "LastPostDateTime",
                    "KeyType": "RANGE"
                }
            ],
            "Projection": {
                "ProjectionType": "KEYS_ONLY"
            }
        }
    ],
    "ProvisionedThroughput": {
        "ReadCapacityUnits": 5,
        "WriteCapacityUnits": 5
    }
}
```

#### Sample Response<a name="API_CreateTable_Example_1_Response"></a>

```
HTTP/1.1 200 OK
x-amzn-RequestId: <RequestId> 
x-amz-crc32: <Checksum>
Content-Type: application/x-amz-json-1.0
Content-Length: <PayloadSizeBytes>
Date: <Date>
 {
    "TableDescription": {
        "TableArn": "arn:aws:dynamodb:us-west-2:123456789012:table/Thread", 
        "AttributeDefinitions": [
            {
                "AttributeName": "ForumName",
                "AttributeType": "S"
            },
            {
                "AttributeName": "LastPostDateTime",
                "AttributeType": "S"
            },
            {
                "AttributeName": "Subject",
                "AttributeType": "S"
            }
        ],
        "CreationDateTime": 1.36372808007E9,
        "ItemCount": 0,
        "KeySchema": [
            {
                "AttributeName": "ForumName",
                "KeyType": "HASH"
            },
            {
                "AttributeName": "Subject",
                "KeyType": "RANGE"
            }
        ],
        "LocalSecondaryIndexes": [
            {
                "IndexArn": "arn:aws:dynamodb:us-west-2:123456789012:table/Thread/index/LastPostIndex", 
                "IndexName": "LastPostIndex",
                "IndexSizeBytes": 0,
                "ItemCount": 0,
                "KeySchema": [
                    {
                        "AttributeName": "ForumName",
                        "KeyType": "HASH"
                    },
                    {
                        "AttributeName": "LastPostDateTime",
                        "KeyType": "RANGE"
                    }
                ],
                "Projection": {
                    "ProjectionType": "KEYS_ONLY"
                }
            }
        ],
        "ProvisionedThroughput": {
            "NumberOfDecreasesToday": 0,
            "ReadCapacityUnits": 5,
            "WriteCapacityUnits": 5
        },
        "TableName": "Thread",
        "TableSizeBytes": 0,
        "TableStatus": "CREATING"
    }
}
```

## See Also<a name="API_CreateTable_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/dynamodb-2012-08-10/CreateTable) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/dynamodb-2012-08-10/CreateTable) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/CreateTable) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/CreateTable) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/CreateTable) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/dynamodb-2012-08-10/CreateTable) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/dynamodb-2012-08-10/CreateTable) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/dynamodb-2012-08-10/CreateTable) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/CreateTable) 