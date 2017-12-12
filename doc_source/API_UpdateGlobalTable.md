# UpdateGlobalTable<a name="API_UpdateGlobalTable"></a>

Adds or removes replicas in the specified global table\. The global table must already exist to be able to use this operation\. Any replica to be added must be empty, must have the same name as the global table, must have the same key schema, must have DynamoDB Streams enabled, and cannot have any local secondary indexes \(LSIs\)\.

**Note**  
Although you can use `UpdateGlobalTable` to add replicas and remove replicas in a single request, for simplicity we recommend that you issue separate requests for adding or removing replicas\.

## Request Syntax<a name="API_UpdateGlobalTable_RequestSyntax"></a>

```
{
   "GlobalTableName": "string",
   "ReplicaUpdates": [ 
      { 
         "Create": { 
            "RegionName": "string"
         },
         "Delete": { 
            "RegionName": "string"
         }
      }
   ]
}
```

## Request Parameters<a name="API_UpdateGlobalTable_RequestParameters"></a>

The request accepts the following data in JSON format\.

**Note**  
In the following list, the required parameters are described first\.

 ** GlobalTableName **   
The global table name\.  
Type: String  
Length Constraints: Minimum length of 3\. Maximum length of 255\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

 ** ReplicaUpdates **   
A list of regions that should be added or removed from the global table\.  
Type: Array of [ReplicaUpdate](API_ReplicaUpdate.md) objects  
Required: Yes

## Response Syntax<a name="API_UpdateGlobalTable_ResponseSyntax"></a>

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

## Response Elements<a name="API_UpdateGlobalTable_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** GlobalTableDescription **   
Contains the details of the global table\.  
Type: [GlobalTableDescription](API_GlobalTableDescription.md) object

## Errors<a name="API_UpdateGlobalTable_Errors"></a>

For information about the errors that are common to all actions, see [Common Errors](CommonErrors.md)\.

 **GlobalTableNotFoundException**   
The specified global table does not exist\.  
HTTP Status Code: 400

 **InternalServerError**   
An error occurred on the server side\.  
HTTP Status Code: 500

 **ReplicaAlreadyExistsException**   
The specified replica is already part of the global table\.  
HTTP Status Code: 400

 **ReplicaNotFoundException**   
The specified replica is no longer part of the global table\.  
HTTP Status Code: 400

 **TableNotFoundException**   
A table with the name `TableName` does not currently exist within the subscriber's account\.  
HTTP Status Code: 400

## See Also<a name="API_UpdateGlobalTable_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/dynamodb-2012-08-10/UpdateGlobalTable) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/dynamodb-2012-08-10/UpdateGlobalTable) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/UpdateGlobalTable) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/UpdateGlobalTable) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/UpdateGlobalTable) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/dynamodb-2012-08-10/UpdateGlobalTable) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/dynamodb-2012-08-10/UpdateGlobalTable) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/dynamodb-2012-08-10/UpdateGlobalTable) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/UpdateGlobalTable) 