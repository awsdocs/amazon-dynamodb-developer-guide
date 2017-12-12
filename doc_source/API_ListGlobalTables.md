# ListGlobalTables<a name="API_ListGlobalTables"></a>

Lists all global tables that have a replica in the specified region\.

## Request Syntax<a name="API_ListGlobalTables_RequestSyntax"></a>

```
{
   "ExclusiveStartGlobalTableName": "string",
   "Limit": number,
   "RegionName": "string"
}
```

## Request Parameters<a name="API_ListGlobalTables_RequestParameters"></a>

The request accepts the following data in JSON format\.

**Note**  
In the following list, the required parameters are described first\.

 ** ExclusiveStartGlobalTableName **   
The first global table name that this operation will evaluate\.  
Type: String  
Length Constraints: Minimum length of 3\. Maximum length of 255\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: No

 ** Limit **   
The maximum number of table names to return\.  
Type: Integer  
Valid Range: Minimum value of 1\.  
Required: No

 ** RegionName **   
Lists the global tables in a specific region\.  
Type: String  
Required: No

## Response Syntax<a name="API_ListGlobalTables_ResponseSyntax"></a>

```
{
   "GlobalTables": [ 
      { 
         "GlobalTableName": "string",
         "ReplicationGroup": [ 
            { 
               "RegionName": "string"
            }
         ]
      }
   ],
   "LastEvaluatedGlobalTableName": "string"
}
```

## Response Elements<a name="API_ListGlobalTables_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** GlobalTables **   
List of global table names\.  
Type: Array of [GlobalTable](API_GlobalTable.md) objects

 ** LastEvaluatedGlobalTableName **   
Last evaluated global table name\.  
Type: String  
Length Constraints: Minimum length of 3\. Maximum length of 255\.  
Pattern: `[a-zA-Z0-9_.-]+` 

## Errors<a name="API_ListGlobalTables_Errors"></a>

For information about the errors that are common to all actions, see [Common Errors](CommonErrors.md)\.

 **InternalServerError**   
An error occurred on the server side\.  
HTTP Status Code: 500

## See Also<a name="API_ListGlobalTables_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/dynamodb-2012-08-10/ListGlobalTables) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/dynamodb-2012-08-10/ListGlobalTables) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/ListGlobalTables) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/ListGlobalTables) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/ListGlobalTables) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/dynamodb-2012-08-10/ListGlobalTables) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/dynamodb-2012-08-10/ListGlobalTables) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/dynamodb-2012-08-10/ListGlobalTables) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/ListGlobalTables) 