# GetItem<a name="API_GetItem_v20111205"></a>

**Important**  
***This section refers to API version 2011\-12\-05, which is deprecated and should not be used for new applications\.***  
 **For documentation on the current low\-level API, see the [Amazon DynamoDB API Reference](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/)\.**

## Description<a name="API_GetItem_Description"></a>

The `GetItem` operation returns a set of `Attributes` for an item that matches the primary key\. If there is no matching item, `GetItem` does not return any data\.

The `GetItem` operation provides an eventually consistent read by default\. If eventually consistent reads are not acceptable for your application, use `ConsistentRead`\. Although this operation might take longer than a standard read, it always returns the last updated value\. For more information, see [Read consistency](HowItWorks.ReadConsistency.md)\.

## Requests<a name="API_GetItem_RequestParameters"></a>

### Syntax<a name="API_GetItem_RequestParameters.syntax"></a>

```
// This header is abbreviated.
// For a sample of a complete header, see DynamoDB low\-level API.
POST / HTTP/1.1 
x-amz-target: DynamoDB_20111205.GetItem
content-type: application/x-amz-json-1.0 

{"TableName":"Table1",
 	"Key": 
		{"HashKeyElement": {"S":"AttributeValue1"},
		"RangeKeyElement": {"N":"AttributeValue2"} 
	},
	"AttributesToGet":["AttributeName3","AttributeName4"],
	"ConsistentRead":Boolean
}
```


****  

|  Name  |  Description  |  Required | 
| --- | --- | --- | 
|  TableName  |  The name of the table containing the requested item\.  Type: String  |  Yes  | 
|  Key  | The primary key values that define the item\. For more information about primary keys, see [Primary key](HowItWorks.CoreComponents.md#HowItWorks.CoreComponents.PrimaryKey)\.Type: Map of `HashKeyElement` to its value and `RangeKeyElement` to its value\. | Yes | 
| AttributesToGet  | Array of Attribute names\. If attribute names are not specified then all attributes will be returned\. If some attributes are not found, they will not appear in the result\.Type: Array | No | 
| ConsistentRead  | If set to `true`, then a consistent read is issued, otherwise eventually consistent is used\.Type: Boolean | No | 

## Responses<a name="API_GetItem_ResponseElements"></a>

### Syntax<a name="API_GetItem_ResponseElements.syntax"></a>

```
HTTP/1.1 200 
x-amzn-RequestId: 8966d095-71e9-11e0-a498-71d736f27375 
content-type: application/x-amz-json-1.0
content-length: 144

{"Item":{
	"AttributeName3":{"S":"AttributeValue3"},
	"AttributeName4":{"N":"AttributeValue4"},
	"AttributeName5":{"B":"dmFsdWU="}
	},
"ConsumedCapacityUnits": 0.5
}
```


****  

|  Name  |  Description  | 
| --- | --- | 
|  Item  | Contains the requested attributes\.Type: Map of attribute name\-value pairs\. | 
| ConsumedCapacityUnits | The number of read capacity units consumed by the operation\. This value shows the number applied toward your provisioned throughput\. Requests for non\-existent items consume the minimum read capacity units, depending on the type of read\. For more information see [Managing settings on DynamoDB provisioned capacity tables](ProvisionedThroughput.md)\. Type: Number | 

## Special errors<a name="API_GetItem_SpecialErrors"></a>

No errors specific to this operation\.

## Examples<a name="API_GetItem_Examples"></a>

 For examples using the AWS SDK, see [Working with items and attributes](WorkingWithItems.md)\.

### Sample request<a name="API_GetItem_Examples_Request"></a>

```
// This header is abbreviated.
// For a sample of a complete header, see DynamoDB low\-level API.
POST / HTTP/1.1 
x-amz-target: DynamoDB_20111205.GetItem
content-type: application/x-amz-json-1.0 

{"TableName":"comptable",
	"Key":
		{"HashKeyElement":{"S":"Julie"},
		"RangeKeyElement":{"N":"1307654345"}},
	"AttributesToGet":["status","friends"],
	"ConsistentRead":true
}
```

### Sample response<a name="API_GetItem_Examples_Response"></a>

Notice the ConsumedCapacityUnits value is 1, because the optional parameter `ConsistentRead` is set to `true`\. If `ConsistentRead` is set to `false` \(or not specified\) for the same request, the response is eventually consistent and the ConsumedCapacityUnits value would be 0\.5\.

```
HTTP/1.1 200 
x-amzn-RequestId: 8966d095-71e9-11e0-a498-71d736f27375 
content-type: application/x-amz-json-1.0
content-length: 72

{"Item":
	{"friends":{"SS":["Lynda, Aaron"]},
	"status":{"S":"online"}
	},
"ConsumedCapacityUnits": 1
}
```