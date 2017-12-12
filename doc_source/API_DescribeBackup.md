# DescribeBackup<a name="API_DescribeBackup"></a>

Describes an existing backup of a table\.

You can call `DescribeBackup` at a maximum rate of 10 times per second\.

## Request Syntax<a name="API_DescribeBackup_RequestSyntax"></a>

```
{
   "BackupArn": "string"
}
```

## Request Parameters<a name="API_DescribeBackup_RequestParameters"></a>

The request accepts the following data in JSON format\.

**Note**  
In the following list, the required parameters are described first\.

 ** BackupArn **   
The ARN associated with the backup\.  
Type: String  
Length Constraints: Minimum length of 37\. Maximum length of 1024\.  
Required: Yes

## Response Syntax<a name="API_DescribeBackup_ResponseSyntax"></a>

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

## Response Elements<a name="API_DescribeBackup_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** BackupDescription **   
Contains the description of the backup created for the table\.  
Type: [BackupDescription](API_BackupDescription.md) object

## Errors<a name="API_DescribeBackup_Errors"></a>

For information about the errors that are common to all actions, see [Common Errors](CommonErrors.md)\.

 **BackupNotFoundException**   
Backup not found for the given BackupARN\.   
HTTP Status Code: 400

 **InternalServerError**   
An error occurred on the server side\.  
HTTP Status Code: 500

## See Also<a name="API_DescribeBackup_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/dynamodb-2012-08-10/DescribeBackup) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/dynamodb-2012-08-10/DescribeBackup) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/DescribeBackup) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/DescribeBackup) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/DescribeBackup) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/dynamodb-2012-08-10/DescribeBackup) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/dynamodb-2012-08-10/DescribeBackup) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/dynamodb-2012-08-10/DescribeBackup) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/DescribeBackup) 