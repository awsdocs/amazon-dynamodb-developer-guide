# RestoreTableFromBackup<a name="API_RestoreTableFromBackup"></a>

Creates a new table from an existing backup\. Any number of users can execute up to 10 concurrent restores in a given account\. 

You can call `RestoreTableFromBackup` at a maximum rate of 10 times per second\.

You must manually set up the following on the restored table:

+ Auto scaling policies

+ IAM policies

+ Cloudwatch metrics and alarms

+ Tags

+ Stream settings

+ Time to Live \(TTL\) settings

## Request Syntax<a name="API_RestoreTableFromBackup_RequestSyntax"></a>

```
{
   "BackupArn": "string",
   "TargetTableName": "string"
}
```

## Request Parameters<a name="API_RestoreTableFromBackup_RequestParameters"></a>

The request accepts the following data in JSON format\.

**Note**  
In the following list, the required parameters are described first\.

 ** BackupArn **   
The ARN associated with the backup\.  
Type: String  
Length Constraints: Minimum length of 37\. Maximum length of 1024\.  
Required: Yes

 ** TargetTableName **   
The name of the new table to which the backup must be restored\.  
Type: String  
Length Constraints: Minimum length of 3\. Maximum length of 255\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

## Response Syntax<a name="API_RestoreTableFromBackup_ResponseSyntax"></a>

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

## Response Elements<a name="API_RestoreTableFromBackup_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** TableDescription **   
The description of the table created from an existing backup\.  
Type: [TableDescription](API_TableDescription.md) object

## Errors<a name="API_RestoreTableFromBackup_Errors"></a>

For information about the errors that are common to all actions, see [Common Errors](CommonErrors.md)\.

 **BackupInUseException**   
There is another ongoing conflicting backup control plane operation on the table\. The backups is either being created, deleted or restored to a table\.  
HTTP Status Code: 400

 **BackupNotFoundException**   
Backup not found for the given BackupARN\.   
HTTP Status Code: 400

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

 **TableAlreadyExistsException**   
A table with the name already exists\.   
HTTP Status Code: 400

 **TableInUseException**   
A table by that name is either being created or deleted\.   
HTTP Status Code: 400

## See Also<a name="API_RestoreTableFromBackup_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/dynamodb-2012-08-10/RestoreTableFromBackup) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/dynamodb-2012-08-10/RestoreTableFromBackup) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/RestoreTableFromBackup) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/RestoreTableFromBackup) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/RestoreTableFromBackup) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/dynamodb-2012-08-10/RestoreTableFromBackup) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/dynamodb-2012-08-10/RestoreTableFromBackup) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/dynamodb-2012-08-10/RestoreTableFromBackup) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/RestoreTableFromBackup) 