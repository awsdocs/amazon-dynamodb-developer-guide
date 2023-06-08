# UpdateTable<a name="API_UpdateTable_v20111205"></a>

**Important**  
***This section refers to API version 2011\-12\-05, which is deprecated and should not be used for new applications\.***  
 **For documentation on the current low\-level API, see the [Amazon DynamoDB API Reference](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/)\.**

## Description<a name="API_updatetable_Description"></a>

Updates the provisioned throughput for the given table\. Setting the throughput for a table helps you manage performance and is part of the provisioned throughput feature of DynamoDB\. For more information, see [Managing settings on DynamoDB provisioned capacity tables](ProvisionedThroughput.md)\.

The provisioned throughput values can be upgraded or downgraded based on the maximums and minimums listed in [Service, account, and table quotas in Amazon DynamoDB](ServiceQuotas.md)\.

The table must be in the `ACTIVE` state for this operation to succeed\. UpdateTable is an asynchronous operation; while executing the operation, the table is in the `UPDATING` state\. While the table is in the `UPDATING` state, the table still has the provisioned throughput from before the call\. The new provisioned throughput setting is in effect only when the table returns to the `ACTIVE` state after the UpdateTable operation\. 

## Requests<a name="API_UpdateTable_RequestParameters"></a>

### Syntax<a name="API_UpdateTable_RequestParameters.syntax"></a>

```
// This header is abbreviated. 
// For a sample of a complete header, see DynamoDB low\-level API.
POST / HTTP/1.1 
x-amz-target: DynamoDB_20111205.UpdateTable
content-type: application/x-amz-json-1.0

{"TableName":"Table1",
    "ProvisionedThroughput":{"ReadCapacityUnits":5,"WriteCapacityUnits":15}
}
```


****  

|  Name  |  Description  |  Required | 
| --- | --- | --- | 
|  TableName  |  The name of the table to update\.  Type: String   |  Yes  | 
|  ProvisionedThroughput  | New throughput for the specified table, consisting of values for `ReadCapacityUnits` and `WriteCapacityUnits`\. See [Managing settings on DynamoDB provisioned capacity tables](ProvisionedThroughput.md)\.Type: Array  | Yes | 
| ProvisionedThroughput :ReadCapacityUnits |  Sets the minimum number of consistent `ReadCapacityUnits` consumed per second for the specified table before DynamoDB balances the load with other operations\.  Eventually consistent read operations require less effort than a consistent read operation, so a setting of 50 consistent `ReadCapacityUnits` per second provides 100 eventually consistent `ReadCapacityUnits` per second\. Type: Number  | Yes | 
| ProvisionedThroughput :WriteCapacityUnits |  Sets the minimum number of `WriteCapacityUnits` consumed per second for the specified table before DynamoDB balances the load with other operations\. Type: Number  | Yes | 

## Responses<a name="API_UpdateTable_ResponseElements"></a>

### Syntax<a name="API_UpdateTable_ResponseElements.syntax"></a>

```
HTTP/1.1 200 OK
x-amzn-RequestId: CSOC7TJPLR0OOKIRLGOHVAICUFVV4KQNSO5AEMVJF66Q9ASUAAJG
Content-Type: application/json
Content-Length: 311
Date: Tue, 12 Jul 2011 21:31:03 GMT

{"TableDescription":
    {"CreationDateTime":1.321657838135E9,
    "KeySchema":
        {"HashKeyElement":{"AttributeName":"AttributeValue1","AttributeType":"S"},
        "RangeKeyElement":{"AttributeName":"AttributeValue2","AttributeType":"N"}},
    "ProvisionedThroughput":
        {"LastDecreaseDateTime":1.321661704489E9,
        "LastIncreaseDateTime":1.321663607695E9,
        "ReadCapacityUnits":5,
        "WriteCapacityUnits":10},
    "TableName":"Table1",
    "TableStatus":"UPDATING"}}
```


****  

|  Name  |  Description  | 
| --- | --- | 
| CreationDateTime | Date when the table was created\.Type: Number | 
|  KeySchema  | The primary key \(simple or composite\) structure for the table\. A name\-value pair for the `HashKeyElement` is required, and a name\-value pair for the `RangeKeyElement` is optional \(only required for composite primary keys\)\. The maximum hash key size is 2048 bytes\. The maximum range key size is 1024 bytes\. Both limits are enforced separately \(i\.e\. you can have a combined hash \+ range 2048 \+ 1024 key\)\. For more information about primary keys, see [Primary key](HowItWorks.CoreComponents.md#HowItWorks.CoreComponents.PrimaryKey)\.Type: Map of `HashKeyElement`, or `HashKeyElement` and `RangeKeyElement` for a composite primary key\. | 
| ProvisionedThroughput | Current throughput settings for the specified table, including values for `LastIncreaseDateTime` \(if applicable\), `LastDecreaseDateTime` \(if applicable\), Type: Array  | 
|  TableName  |  The name of the updated table\. Type: String  | 
|  TableStatus  | The current state of the table \(CREATING, ACTIVE, DELETING or UPDATING\), which should be UPDATING\. Use the [DescribeTables](API_DescribeTables_v20111205.md) operationto check the status of the table\. Type: String | 

## Special errors<a name="API_UpdateTable_SpecialErrors"></a>


****  

|  Error  |  Description  | 
| --- | --- | 
| ResourceNotFoundException  | The specified table was not found\.  | 
| ResourceInUseException | The table is not in the ACTIVE state\. | 

## Examples<a name="API_UpdateTable_Examples"></a>

### Sample request<a name="API_UpdateTable_Examples_Request"></a>

```
// This header is abbreviated. 
// For a sample of a complete header, see DynamoDB low\-level API.
POST / HTTP/1.1 
x-amz-target: DynamoDB_20111205.UpdateTable
content-type: application/x-amz-json-1.0

{"TableName":"comp1",
    "ProvisionedThroughput":{"ReadCapacityUnits":5,"WriteCapacityUnits":15}
}
```

### Sample response<a name="API_UpdateTable_Examples_Response"></a>

```
HTTP/1.1 200 OK
content-type: application/x-amz-json-1.0
content-length: 390
Date: Sat, 19 Nov 2011 00:46:47 GMT

{"TableDescription":
    {"CreationDateTime":1.321657838135E9,
    "KeySchema":
        {"HashKeyElement":{"AttributeName":"user","AttributeType":"S"},
        "RangeKeyElement":{"AttributeName":"time","AttributeType":"N"}},
    "ProvisionedThroughput":
        {"LastDecreaseDateTime":1.321661704489E9,
        "LastIncreaseDateTime":1.321663607695E9,
        "ReadCapacityUnits":5,
        "WriteCapacityUnits":10},
    "TableName":"comp1",
    "TableStatus":"UPDATING"}
}
```

## Related actions<a name="API_UpdateTable_Related_Actions"></a>
+  [CreateTable](API_CreateTable_v20111205.md) 
+  [DescribeTables](API_DescribeTables_v20111205.md) 
+  [DeleteTable](API_DeleteTable_v20111205.md) 