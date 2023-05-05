# DescribeTables<a name="API_DescribeTables_v20111205"></a>

**Important**  
***This section refers to API version 2011\-12\-05, which is deprecated and should not be used for new applications\.***  
 **For documentation on the current low\-level API, see the [Amazon DynamoDB API Reference](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/)\.**

## Description<a name="API_DescribeTables_Description"></a>

Returns information about the table, including the current status of the table, the primary key schema and when the table was created\. DescribeTable results are eventually consistent\. If you use DescribeTable too early in the process of creating a table, DynamoDB returns a `ResourceNotFoundException`\. If you use DescribeTable too early in the process of updating a table, the new values might not be immediately available\.

## Requests<a name="API_DescribeTables_RequestParameters"></a>

### Syntax<a name="API_DescribeTables_RequestParameters.syntax"></a>

```
// This header is abbreviated. 
// For a sample of a complete header, see DynamoDB low\-level API.
POST / HTTP/1.1 
x-amz-target: DynamoDB_20111205.DescribeTable
content-type: application/x-amz-json-1.0

{"TableName":"Table1"}
```


****  

|  Name  |  Description  |  Required | 
| --- | --- | --- | 
|  TableName  |  The name of the table to describe\.  Type: String   |  Yes  | 

## Responses<a name="API_DescribeTables_ResponseElements"></a>

### Syntax<a name="API_DescribeTables_ResponseElements.syntax"></a>

```
HTTP/1.1 200 
x-amzn-RequestId: 8966d095-71e9-11e0-a498-71d736f27375
content-type: application/x-amz-json-1.0
Content-Length: 543


{"Table":
    {"CreationDateTime":1.309988345372E9,
    ItemCount:1,
    "KeySchema":
        {"HashKeyElement":{"AttributeName":"AttributeName1","AttributeType":"S"},
        "RangeKeyElement":{"AttributeName":"AttributeName2","AttributeType":"N"}},
    "ProvisionedThroughput":{"LastIncreaseDateTime": Date, "LastDecreaseDateTime": Date, "ReadCapacityUnits":10,"WriteCapacityUnits":10},
    "TableName":"Table1",
    "TableSizeBytes":1,
    "TableStatus":"ACTIVE"
    }
}
```


****  

|  Name  |  Description  | 
| --- | --- | 
|  Table  |  Container for the table being described\. Type: String  | 
| CreationDateTime | Date when the table was created in [UNIX epoch time](http://www.epochconverter.com/)\. | 
|  ItemCount  |  Number of items in the specified table\. DynamoDB updates this value approximately every six hours\. Recent changes might not be reflected in this value\. Type: Number  | 
|  KeySchema  | The primary key \(simple or composite\) structure for the table\. A name\-value pair for the HashKeyElement is required, and a name\-value pair for the RangeKeyElement is optional \(only required for composite primary keys\)\. The maximum hash key size is 2048 bytes\. The maximum range key size is 1024 bytes\. Both limits are enforced separately \(i\.e\. you can have a combined hash \+ range 2048 \+ 1024 key\)\. For more information about primary keys, see [Primary key](HowItWorks.CoreComponents.md#HowItWorks.CoreComponents.PrimaryKey) \. | 
| ProvisionedThroughput  | Throughput for the specified table, consisting of values for LastIncreaseDateTime \(if applicable\), LastDecreaseDateTime \(if applicable\), ReadCapacityUnits and WriteCapacityUnits\. If the throughput for the table has never been increased or decreased, DynamoDB does not return values for those elements\. See [Managing settings on DynamoDB provisioned capacity tables](ProvisionedThroughput.md)\.Type: Array  | 
|  TableName  |  The name of the requested table\.  Type: String  | 
|  TableSizeBytes  |  Total size of the specified table, in bytes\. DynamoDB updates this value approximately every six hours\. Recent changes might not be reflected in this value\. Type: Number  | 
|  TableStatus  | The current state of the table \(CREATING, ACTIVE, DELETING or UPDATING\)\. Once the table is in the ACTIVE state, you can add data\.  | 

## Special errors<a name="API_DescribeTables_SpecialErrors"></a>

No errors are specific to this operation\.

## Examples<a name="API_DescribeTables_Examples"></a>

 The following examples show an HTTP POST request and response using the DescribeTable operation for a table named "comp\-table"\. The table has a composite primary key\.

### Sample Request<a name="API_DescribeTables_Examples_Request"></a>

```
// This header is abbreviated. 
// For a sample of a complete header, see DynamoDB low\-level API.
POST / HTTP/1.1 
x-amz-target: DynamoDB_20111205.DescribeTable
content-type: application/x-amz-json-1.0

{"TableName":"users"}
```

### Sample response<a name="API_DescribeTables_Examples_Response"></a>

```
HTTP/1.1 200 
x-amzn-RequestId: 8966d095-71e9-11e0-a498-71d736f27375
content-type: application/x-amz-json-1.0
content-length: 543

{"Table":
    {"CreationDateTime":1.309988345372E9,
    "ItemCount":23,
    "KeySchema":
        {"HashKeyElement":{"AttributeName":"user","AttributeType":"S"},
        "RangeKeyElement":{"AttributeName":"time","AttributeType":"N"}},
    "ProvisionedThroughput":{"LastIncreaseDateTime": 1.309988345384E9, "ReadCapacityUnits":10,"WriteCapacityUnits":10},
    "TableName":"users",
    "TableSizeBytes":949,
    "TableStatus":"ACTIVE"
    }
}
```

## Related actions<a name="API_DescribeTables_Related_Actions"></a>
+  [CreateTable](API_CreateTable_v20111205.md) 
+  [DeleteTable](API_DeleteTable_v20111205.md) 
+  [ListTables](API_ListTables_v20111205.md) 