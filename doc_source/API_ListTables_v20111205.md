# ListTables<a name="API_ListTables_v20111205"></a>

**Important**  
***This section refers to API version 2011\-12\-05, which is deprecated and should not be used for new applications\.***  
 **For documentation on the current low\-level API, see the [Amazon DynamoDB API Reference](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/)\.**

## Description<a name="API_ListTables_Description"></a>

Returns an array of all the tables associated with the current account and endpoint\.

Each DynamoDB endpoint is entirely independent\. For example, if you have two tables called "MyTable," one in dynamodb\.us\-west\-2\.amazonaws\.com and one in dynamodb\.us\-east\-1\.amazonaws\.com, they are completely independent and do not share any data\. The ListTables operation returns all of the table names associated with the account making the request, for the endpoint that receives the request\.

## Requests<a name="API_ListTables_RequestParameters"></a>

### Syntax<a name="API_ListTables_RequestParameters.syntax"></a>

```
// This header is abbreviated. 
// For a sample of a complete header, see DynamoDB Low\-Level API.
POST / HTTP/1.1 
x-amz-target: DynamoDB_20111205.ListTables
content-type: application/x-amz-json-1.0

{"ExclusiveStartTableName":"Table1","Limit":3}
```

The ListTables operation, by default, requests all of the table names associated with the account making the request, for the endpoint that receives the request\.


****  

|  Name  |  Description  | Required | 
| --- | --- | --- | 
|  Limit  |  A number of maximum table names to return\.  Type: Integer  | No | 
| ExclusiveStartTableName  | The name of the table that starts the list\. If you already ran a ListTables operation and received an `LastEvaluatedTableName` value in the response, use that value here to continue the list\. Type: String | No | 

## Responses<a name="API_ListTables_ResponseElements"></a>

### Syntax<a name="API_ListTables_ResponseElements.syntax"></a>

```
HTTP/1.1 200 OK
x-amzn-RequestId: S1LEK2DPQP8OJNHVHL8OU2M7KRVV4KQNSO5AEMVJF66Q9ASUAAJG
content-type: application/x-amz-json-1.0
content-length: 81
Date: Fri, 21 Oct 2011 20:35:38 GMT

{"TableNames":["Table1","Table2","Table3"], "LastEvaluatedTableName":"Table3"}
```


****  

|  Name  |  Description  | 
| --- | --- | 
|  TableNames  |  The names of the tables associated with the current account at the current endpoint\.  Type: Array  | 
| LastEvaluatedTableName  | The name of the last table in the current list, only if some tables for the account and endpoint have not been returned\. This value does not exist in a response if all table names are already returned\. Use this value as the `ExclusiveStartTableName` in a new request to continue the list until all the table names are returned\.  Type: String  | 

## Special Errors<a name="API_ListTables_SpecialErrors"></a>

No errors are specific to this operation\.

## Examples<a name="API_ListTables_Examples"></a>

The following examples show an HTTP POST request and response using the ListTables operation\.

### Sample Request<a name="API_ListTables_Examples_Request"></a>

```
// This header is abbreviated. 
// For a sample of a complete header, see DynamoDB Low\-Level API.
POST / HTTP/1.1 
x-amz-target: DynamoDB_20111205.ListTables
content-type: application/x-amz-json-1.0

{"ExclusiveStartTableName":"comp2","Limit":3}
```

### Sample Response<a name="API_ListTables_Examples_Response"></a>

```
HTTP/1.1 200 OK
x-amzn-RequestId: S1LEK2DPQP8OJNHVHL8OU2M7KRVV4KQNSO5AEMVJF66Q9ASUAAJG
content-type: application/x-amz-json-1.0
content-length: 81
Date: Fri, 21 Oct 2011 20:35:38 GMT

{"LastEvaluatedTableName":"comp5","TableNames":["comp3","comp4","comp5"]}
```

## Related Actions<a name="API_ListTables_Related_Actions"></a>
+  [DescribeTables](API_DescribeTables_v20111205.md) 
+  [CreateTable](API_CreateTable_v20111205.md) 
+  [DeleteTable](API_DeleteTable_v20111205.md) 