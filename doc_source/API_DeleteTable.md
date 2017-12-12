# DeleteTable<a name="API_DeleteTable"></a>

The `DeleteTable` operation deletes a table and all of its items\. After a `DeleteTable` request, the specified table is in the `DELETING` state until DynamoDB completes the deletion\. If the table is in the `ACTIVE` state, you can delete it\. If a table is in `CREATING` or `UPDATING` states, then DynamoDB returns a `ResourceInUseException`\. If the specified table does not exist, DynamoDB returns a `ResourceNotFoundException`\. If table is already in the `DELETING` state, no error is returned\. 

**Note**  
DynamoDB might continue to accept data read and write operations, such as `GetItem` and `PutItem`, on a table in the `DELETING` state until the table deletion is complete\.

When you delete a table, any indexes on that table are also deleted\.

If you have DynamoDB Streams enabled on the table, then the corresponding stream on that table goes into the `DISABLED` state, and the stream is automatically deleted after 24 hours\.

Use the `DescribeTable` action to check the status of the table\. 

## Request Syntax<a name="API_DeleteTable_RequestSyntax"></a>

```
{
   "TableName": "string"
}
```

## Request Parameters<a name="API_DeleteTable_RequestParameters"></a>

The request accepts the following data in JSON format\.

**Note**  
In the following list, the required parameters are described first\.

 ** TableName **   
The name of the table to delete\.  
Type: String  
Length Constraints: Minimum length of 3\. Maximum length of 255\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

## Response Syntax<a name="API_DeleteTable_ResponseSyntax"></a>

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

## Response Elements<a name="API_DeleteTable_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** TableDescription **   
Represents the properties of a table\.  
Type: [TableDescription](API_TableDescription.md) object

## Errors<a name="API_DeleteTable_Errors"></a>

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

 **ResourceNotFoundException**   
The operation tried to access a nonexistent table or index\. The resource might not be specified correctly, or its status might not be `ACTIVE`\.  
HTTP Status Code: 400

## Example<a name="API_DeleteTable_Examples"></a>

### Delete a Table<a name="API_DeleteTable_Example_1"></a>

This example deletes the Reply table\.

#### Sample Request<a name="API_DeleteTable_Example_1_Request"></a>

```
POST / HTTP/1.1
Host: dynamodb.<region>.<domain>;
Accept-Encoding: identity
Content-Length: <PayloadSizeBytes>     
User-Agent: <UserAgentString>
Content-Type: application/x-amz-json-1.0
Authorization: AWS4-HMAC-SHA256 Credential=<Credential>, SignedHeaders=<Headers>, Signature=<Signature>
X-Amz-Date: <Date> 
X-Amz-Target: DynamoDB_20120810.DeleteTable 

{
    "TableName": "Reply"
}
```

#### Sample Response<a name="API_DeleteTable_Example_1_Response"></a>

```
HTTP/1.1 200 OK
x-amzn-RequestId: <RequestId> 
x-amz-crc32: <Checksum>
Content-Type: application/x-amz-json-1.0
Content-Length: <PayloadSizeBytes>
Date: <Date>
 {
    "TableDescription": {
        "TableArn": "arn:aws:dynamodb:us-west-2:123456789012:table/Reply", 
        "ItemCount": 0,
        "ProvisionedThroughput": {
            "NumberOfDecreasesToday": 0,
            "ReadCapacityUnits": 5,
            "WriteCapacityUnits": 5
        },
        "TableName": "Reply",
        "TableSizeBytes": 0,
        "TableStatus": "DELETING"
    }
}
```

## See Also<a name="API_DeleteTable_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/dynamodb-2012-08-10/DeleteTable) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/dynamodb-2012-08-10/DeleteTable) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/DeleteTable) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/DeleteTable) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/DeleteTable) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/dynamodb-2012-08-10/DeleteTable) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/dynamodb-2012-08-10/DeleteTable) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/dynamodb-2012-08-10/DeleteTable) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/DeleteTable) 