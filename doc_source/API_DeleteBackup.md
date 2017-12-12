# DeleteBackup<a name="API_DeleteBackup"></a>

Deletes an existing backup of a table\.

You can call `DeleteBackup` at a maximum rate of 10 times per second\.

## Request Syntax<a name="API_DeleteBackup_RequestSyntax"></a>

```
{
   "BackupArn": "string"
}
```

## Request Parameters<a name="API_DeleteBackup_RequestParameters"></a>

The request accepts the following data in JSON format\.

**Note**  
In the following list, the required parameters are described first\.

 ** BackupArn **   
The ARN associated with the backup\.  
Type: String  
Length Constraints: Minimum length of 37\. Maximum length of 1024\.  
Required: Yes

## Response Syntax<a name="API_DeleteBackup_ResponseSyntax"></a>

```
{
   "BackupDescription": { 
      "BackupDetails": { 
         "BackupArn": "string",
         "BackupCreationDateTime": number,
         "BackupName": "string",
         "BackupSizeBytes": number,
         "BackupStatus": "string"
      },
      "SourceTableDetails": { 
         "ItemCount": number,
         "KeySchema": [ 
            { 
               "AttributeName": "string",
               "KeyType": "string"
            }
         ],
         "ProvisionedThroughput": { 
            "ReadCapacityUnits": number,
            "WriteCapacityUnits": number
         },
         "TableArn": "string",
         "TableCreationDateTime": number,
         "TableId": "string",
         "TableName": "string",
         "TableSizeBytes": number
      },
      "SourceTableFeatureDetails": { 
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
         "StreamDescription": { 
            "StreamEnabled": boolean,
            "StreamViewType": "string"
         },
         "TimeToLiveDescription": { 
            "AttributeName": "string",
            "TimeToLiveStatus": "string"
         }
      }
   }
}
```

## Response Elements<a name="API_DeleteBackup_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** BackupDescription **   
Contains the description of the backup created for the table\.  
Type: [BackupDescription](API_BackupDescription.md) object

## Errors<a name="API_DeleteBackup_Errors"></a>

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

## See Also<a name="API_DeleteBackup_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/dynamodb-2012-08-10/DeleteBackup) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/dynamodb-2012-08-10/DeleteBackup) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/DeleteBackup) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/DeleteBackup) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/DeleteBackup) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/dynamodb-2012-08-10/DeleteBackup) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/dynamodb-2012-08-10/DeleteBackup) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/dynamodb-2012-08-10/DeleteBackup) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/DeleteBackup) 