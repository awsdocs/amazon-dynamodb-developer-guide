# CreateTable<a name="API_CreateTable_v20111205"></a>

**Important**  
***This section refers to API version 2011\-12\-05, which is deprecated and should not be used for new applications\.***  
 **For documentation on the current low\-level API, see the [Amazon DynamoDB API Reference](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/)\.**

## Description<a name="API_CreateTable_Description"></a>

The `CreateTable` operation adds a new table to your account\.

The table name must be unique among those associated with the AWS Account issuing the request, and the AWS region that receives the request \(such as dynamodb\.us\-west\-2\.amazonaws\.com\)\. Each DynamoDB endpoint is entirely independent\. For example, if you have two tables called "MyTable," one in dynamodb\.us\-west\-2\.amazonaws\.com and one in dynamodb\.us\-west\-1\.amazonaws\.com, they are completely independent and do not share any data\.

The `CreateTable` operation triggers an asynchronous workflow to begin creating the table\. DynamoDB immediately returns the state of the table \(`CREATING`\) until the table is in the `ACTIVE` state\. Once the table is in the `ACTIVE` state, you can perform data plane operations\. 

Use the [DescribeTables](API_DescribeTables_v20111205.md) operation to check the status of the table\. 

## Requests<a name="API_CreateTable_RequestParameters"></a>

### Syntax<a name="API_CreateTable_RequestParameters.syntax"></a>

```
// This header is abbreviated. 
// For a sample of a complete header, see DynamoDB low\-level API.
POST / HTTP/1.1 
x-amz-target: DynamoDB_20111205.CreateTable 
content-type: application/x-amz-json-1.0 

{"TableName":"Table1",
    "KeySchema":
        {"HashKeyElement":{"AttributeName":"AttributeName1","AttributeType":"S"},
        "RangeKeyElement":{"AttributeName":"AttributeName2","AttributeType":"N"}},
    "ProvisionedThroughput":{"ReadCapacityUnits":5,"WriteCapacityUnits":10}
}
```


****  

|  Name  |  Description  |  Required | 
| --- | --- | --- | 
|  TableName  |  The name of the table to create\.Allowed characters are a\-z, A\-Z, 0\-9, '\_' \(underscore\), '\-' \(dash\), and '\.' \(dot\)\. Names can be between 3 and 255 characters long\. Type: String  |  Yes  | 
|  KeySchema  | The primary key \(simple or composite\) structure for the table\. A name\-value pair for the `HashKeyElement` is required, and a name\-value pair for the `RangeKeyElement` is optional \(only required for composite primary keys\)\. For more information about primary keys, see [Primary key](HowItWorks.CoreComponents.md#HowItWorks.CoreComponents.PrimaryKey)\.Primary key element names can be between 1 and 255 characters long with no character restrictions\. Possible values for the AttributeType are "S" \(string\), "N" \(numeric\), or "B" \(binary\)\.Type: Map of `HashKeyElement`, or `HashKeyElement` and `RangeKeyElement` for a composite primary key\. | Yes | 
|  ProvisionedThroughput  | New throughput for the specified table, consisting of values for ReadCapacityUnits and WriteCapacityUnits\. For details, see [Managing settings on DynamoDB provisioned capacity tables](ProvisionedThroughput.md)\.  For current maximum/minimum values, see [Service, account, and table quotas in Amazon DynamoDB](ServiceQuotas.md)\. Type: Array  | Yes | 
| ProvisionedThroughput: ReadCapacityUnits |  Sets the minimum number of consistent `ReadCapacityUnits` consumed per second for the specified table before DynamoDB balances the load with other operations\.  Eventually consistent read operations require less effort than a consistent read operation, so a setting of 50 consistent `ReadCapacityUnits` per second provides 100 eventually consistent `ReadCapacityUnits` per second\. Type: Number  | Yes | 
| ProvisionedThroughput: WriteCapacityUnits | Sets the minimum number of WriteCapacityUnits consumed per second for the specified table before DynamoDB balances the load with other operations\. Type: Number  | Yes | 

## Responses<a name="API_CreateTable_CommonResponseElements"></a>

### Syntax<a name="API_CreateTable_CommonResponseElements.syntax"></a>

```
HTTP/1.1 200 OK
x-amzn-RequestId: CSOC7TJPLR0OOKIRLGOHVAICUFVV4KQNSO5AEMVJF66Q9ASUAAJG
content-type: application/x-amz-json-1.0
content-length: 311
Date: Tue, 12 Jul 2011 21:31:03 GMT

{"TableDescription":
    {"CreationDateTime":1.310506263362E9,
    "KeySchema":
        {"HashKeyElement":{"AttributeName":"AttributeName1","AttributeType":"S"},
        "RangeKeyElement":{"AttributeName":"AttributeName2","AttributeType":"N"}},
    "ProvisionedThroughput":{"ReadCapacityUnits":5,"WriteCapacityUnits":10},
    "TableName":"Table1",
    "TableStatus":"CREATING"
    }
}
```


****  

|  Name  |  Description  | 
| --- | --- | 
| TableDescription  | A container for the table properties\. | 
| CreationDateTime | Date when the table was created in [UNIX epoch time](http://www.epochconverter.com/)\.Type: Number | 
| KeySchema  | The primary key \(simple or composite\) structure for the table\. A name\-value pair for the `HashKeyElement` is required, and a name\-value pair for the `RangeKeyElement` is optional \(only required for composite primary keys\)\. For more information about primary keys, see [Primary key](HowItWorks.CoreComponents.md#HowItWorks.CoreComponents.PrimaryKey) \.Type: Map of `HashKeyElement`, or `HashKeyElement` and `RangeKeyElement` for a composite primary key\. | 
| ProvisionedThroughput  |  Throughput for the specified table, consisting of values for `ReadCapacityUnits` and `WriteCapacityUnits`\. See [Managing settings on DynamoDB provisioned capacity tables](ProvisionedThroughput.md)\. Type: Array   | 
| ProvisionedThroughput :ReadCapacityUnits |  The minimum number of `ReadCapacityUnits` consumed per second before DynamoDB\. balances the load with other operationsType: Number  | 
| ProvisionedThroughput :WriteCapacityUnits |  The minimum number of `ReadCapacityUnits` consumed per second before `WriteCapacityUnits`\. balances the load with other operationsType: Number  | 
|  TableName  |  The name of the created table\. Type: String  | 
|  TableStatus  | The current state of the table \(`CREATING`\)\. Once the table is in the `ACTIVE` state, you can put data in it\.Use the [DescribeTables](API_DescribeTables_v20111205.md) API to check the status of the table\.Type: String | 

## Special errors<a name="API_CreateTable_SpecialErrors"></a>


****  

|  Error  |  Description  | 
| --- | --- | 
|  ResourceInUseException  | Attempt to recreate an already existing table\. | 
|  LimitExceededException  | The number of simultaneous table requests \(cumulative number of tables in the `CREATING`, `DELETING` or `UPDATING` state\) exceeds the maximum allowed\. For current maximum/minimum values, see [Service, account, and table quotas in Amazon DynamoDB](ServiceQuotas.md)\. \.  | 

## Examples<a name="API_CreateTable_Examples"></a>

The following example creates a table with a composite primary key containing a string and a number\. For examples using the AWS SDK, see [Working with tables and data in DynamoDB](WorkingWithTables.md)\.

### Sample request<a name="API_CreateTable_Examples_Request"></a>

```
// This header is abbreviated. 
// For a sample of a complete header, see DynamoDB low\-level API.
POST / HTTP/1.1 
x-amz-target: DynamoDB_20111205.CreateTable 
content-type: application/x-amz-json-1.0


{"TableName":"comp-table",
    "KeySchema":
        {"HashKeyElement":{"AttributeName":"user","AttributeType":"S"},
        "RangeKeyElement":{"AttributeName":"time","AttributeType":"N"}},
    "ProvisionedThroughput":{"ReadCapacityUnits":5,"WriteCapacityUnits":10}
}
```

### Sample response<a name="API_CreateTable_Examples_Response"></a>

```
HTTP/1.1 200 OK
x-amzn-RequestId: CSOC7TJPLR0OOKIRLGOHVAICUFVV4KQNSO5AEMVJF66Q9ASUAAJG
content-type: application/x-amz-json-1.0
content-length: 311
Date: Tue, 12 Jul 2011 21:31:03 GMT

{"TableDescription":
    {"CreationDateTime":1.310506263362E9,
    "KeySchema":
        {"HashKeyElement":{"AttributeName":"user","AttributeType":"S"},
        "RangeKeyElement":{"AttributeName":"time","AttributeType":"N"}},
    "ProvisionedThroughput":{"ReadCapacityUnits":5,"WriteCapacityUnits":10},
    "TableName":"comp-table",
    "TableStatus":"CREATING"
    }
}
```

## Related actions<a name="API_CreateTable_Related_Actions"></a>
+  [DescribeTables](API_DescribeTables_v20111205.md) 
+  [DeleteTable](API_DeleteTable_v20111205.md)