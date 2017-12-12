# CreateGlobalTable<a name="API_CreateGlobalTable"></a>

Creates a global table from an existing table\. A global table creates a replication relationship between two or more DynamoDB tables with the same table name in the provided regions\. 

 Tables can only be added as the replicas of a global table group under the following conditions: 

+  The tables must have the same name\. 

+  The tables must contain no items\. 

+  The tables must have the same hash key and sort key \(if present\)\. 

+  The tables must have DynamoDB Streams enabled \(NEW\_AND\_OLD\_IMAGES\)\. 

## Request Syntax<a name="API_CreateGlobalTable_RequestSyntax"></a>

```
{
   "GlobalTableName": "string",
   "ReplicationGroup": [ 
      { 
         "RegionName": "string"
      }
   ]
}
```

## Request Parameters<a name="API_CreateGlobalTable_RequestParameters"></a>

The request accepts the following data in JSON format\.

**Note**  
In the following list, the required parameters are described first\.

 ** GlobalTableName **   
The global table name\.  
Type: String  
Length Constraints: Minimum length of 3\. Maximum length of 255\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

 ** ReplicationGroup **   
The regions where the global table needs to be created\.  
Type: Array of [Replica](API_Replica.md) objects  
Required: Yes

## Response Syntax<a name="API_CreateGlobalTable_ResponseSyntax"></a>

```
{
   "GlobalTableDescription": { 
      "CreationDateTime": number,
      "GlobalTableArn": "string",
      "GlobalTableName": "string",
      "GlobalTableStatus": "string",
      "ReplicationGroup": [ 
         { 
            "RegionName": "string"
         }
      ]
   }
}
```

## Response Elements<a name="API_CreateGlobalTable_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** GlobalTableDescription **   
Contains the details of the global table\.  
Type: [GlobalTableDescription](API_GlobalTableDescription.md) object

## Errors<a name="API_CreateGlobalTable_Errors"></a>

For information about the errors that are common to all actions, see [Common Errors](CommonErrors.md)\.

 **GlobalTableAlreadyExistsException**   
The specified global table already exists\.  
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

 **TableNotFoundException**   
A table with the name `TableName` does not currently exist within the subscriber's account\.  
HTTP Status Code: 400

## See Also<a name="API_CreateGlobalTable_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/dynamodb-2012-08-10/CreateGlobalTable) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/dynamodb-2012-08-10/CreateGlobalTable) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/CreateGlobalTable) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/CreateGlobalTable) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/CreateGlobalTable) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/dynamodb-2012-08-10/CreateGlobalTable) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/dynamodb-2012-08-10/CreateGlobalTable) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/dynamodb-2012-08-10/CreateGlobalTable) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/CreateGlobalTable) 