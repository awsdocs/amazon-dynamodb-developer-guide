# PutItem<a name="API_PutItem_v20111205"></a>

**Important**  
***This section refers to API version 2011\-12\-05, which is deprecated and should not be used for new applications\.***  
 **For documentation on the current low\-level API, see the [Amazon DynamoDB API Reference](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/)\.**

## Description<a name="API_PutItem_Description"></a>

Creates a new item, or replaces an old item with a new item \(including all the attributes\)\. If an item already exists in the specified table with the same primary key, the new item completely replaces the existing item\. You can perform a conditional put \(insert a new item if one with the specified primary key doesn't exist\), or replace an existing item if it has certain attribute values\. 

Attribute values may not be null; string and binary type attributes must have lengths greater than zero; and set type attributes must not be empty\. Requests with empty values will be rejected with a `ValidationException`\.

**Note**  
To ensure that a new item does not replace an existing item, use a conditional put operation with `Exists` set to `false` for the primary key attribute, or attributes\.

For more information about using `PutItem`, see [Working with items and attributes](WorkingWithItems.md)\.

## Requests<a name="API_PutItem_RequestParameters"></a>

### Syntax<a name="API_PutItem_RequestParameters.syntax"></a>

```
// This header is abbreviated.
// For a sample of a complete header, see DynamoDB low\-level API.
POST / HTTP/1.1 
x-amz-target: DynamoDB_20111205.PutItem
content-type: application/x-amz-json-1.0

{"TableName":"Table1",
    "Item":{
        "AttributeName1":{"S":"AttributeValue1"},
        "AttributeName2":{"N":"AttributeValue2"},
        "AttributeName5":{"B":"dmFsdWU="}
    },
    "Expected":{"AttributeName3":{"Value": {"S":"AttributeValue"}, "Exists":Boolean}},
    "ReturnValues":"ReturnValuesConstant"}
```


****  

|  Name  |  Description  |  Required | 
| --- | --- | --- | 
|  TableName  |  The name of the table to contain the item\. Type: String  |  Yes  | 
|  Item  | A map of the attributes for the item, and must include the primary key values that define the item\. Other attribute name\-value pairs can be provided for the item\. For more information about primary keys, see [Primary key](HowItWorks.CoreComponents.md#HowItWorks.CoreComponents.PrimaryKey)\.Type: Map of attribute names to attribute values\. | Yes | 
| Expected  | Designates an attribute for a conditional put\. The `Expected` parameter allows you to provide an attribute name, and whether or not DynamoDB should check to see if the attribute value already exists; or if the attribute value exists and has a particular value before changing it\.Type: Map of an attribute names to an attribute value, and whether it exists\. | No | 
| Expected:AttributeName  | The name of the attribute for the conditional put\.Type: String | No | 
| Expected:AttributeName: ExpectedAttributeValue | Use this parameter to specify whether or not a value already exists for the attribute name\-value pair\. The following JSON notation replaces the item if the "Color" attribute doesn't already exist for that item:<pre>"Expected" :<br />	{"Color":{"Exists":false}}</pre>The following JSON notation checks to see if the attribute with name "Color" has an existing value of "Yellow" before replacing the item: <pre>"Expected" : <br />	{"Color":{"Exists":true,{"Value":{"S":"Yellow"}}}</pre>By default, if you use the `Expected` parameter and provide a `Value`, DynamoDB assumes the attribute exists and has a current value to be replaced\. So you don't have to specify `{"Exists":true}`, because it is implied\. You can shorten the request to:<pre>"Expected" : <br />	{"Color":{"Value":{"S":"Yellow"}}}</pre> If you specify `{"Exists":true}` without an attribute value to check, DynamoDB returns an error\.  | No | 
| ReturnValues  | Use this parameter if you want to get the attribute name\-value pairs before they were updated with the `PutItem` request\. Possible parameter values are `NONE` \(default\) or `ALL_OLD`\. If `ALL_OLD` is specified, and `PutItem` overwrote an attribute name\-value pair, the content of the old item is returned\. If this parameter is not provided or is `NONE`, nothing is returned\.Type: String | No | 

## Responses<a name="API_PutItem_CommonResponseElements"></a>

### Syntax<a name="API_PutItem_CommonResponseElements.syntax"></a>

The following syntax example assumes the request specified a `ReturnValues` parameter of `ALL_OLD`; otherwise, the response has only the `ConsumedCapacityUnits` element\.

```
HTTP/1.1 200 
x-amzn-RequestId: 8966d095-71e9-11e0-a498-71d736f27375 
content-type: application/x-amz-json-1.0
content-length: 85

{"Attributes":
	{"AttributeName3":{"S":"AttributeValue3"},
	"AttributeName2":{"SS":"AttributeValue2"},
	"AttributeName1":{"SS":"AttributeValue1"},
	},
"ConsumedCapacityUnits":1
}
```


****  

|  Name  |  Description  | 
| --- | --- | 
|  Attributes  | Attribute values before the put operation, but only if the `ReturnValues` parameter is specified as `ALL_OLD` in the request\.Type: Map of attribute name\-value pairs\. | 
| ConsumedCapacityUnits | The number of write capacity units consumed by the operation\. This value shows the number applied toward your provisioned throughput\. For more information see [Managing settings on DynamoDB provisioned capacity tables](ProvisionedThroughput.md)\. Type: Number | 

## Special errors<a name="API_PutItem_SpecialErrors"></a>


****  

|  Error  |  Description  | 
| --- | --- | 
|  ConditionalCheckFailedException  | Conditional check failed\. An expected attribute value was not found\.  | 
| ResourceNotFoundException  | The specified item or attribute was not found\. | 

## Examples<a name="API_PutItem_Examples"></a>

For examples using the AWS SDK, see [Working with items and attributes](WorkingWithItems.md)\.

### Sample request<a name="API_PutItem_Examples_Request"></a>

```
// This header is abbreviated. For a sample of a complete header, see DynamoDB low\-level API.
POST / HTTP/1.1 
x-amz-target: DynamoDB_20111205.PutItem 
content-type: application/x-amz-json-1.0

{"TableName":"comp5",
	"Item":
		{"time":{"N":"300"},
		"feeling":{"S":"not surprised"},
		"user":{"S":"Riley"}
		},
	"Expected":
		{"feeling":{"Value":{"S":"surprised"},"Exists":true}}
	"ReturnValues":"ALL_OLD"
}
```

### Sample response<a name="API_PutItem_Examples_Response"></a>

```
HTTP/1.1 200 
x-amzn-RequestId: 8952fa74-71e9-11e0-a498-71d736f27375 
content-type: application/x-amz-json-1.0
content-length: 84

{"Attributes":
	{"feeling":{"S":"surprised"},
	"time":{"N":"300"},
	"user":{"S":"Riley"}},
"ConsumedCapacityUnits":1
}
```

## Related actions<a name="API_PutItem_Related_Actions"></a>
+  [UpdateItem](API_UpdateItem_v20111205.md) 
+  [DeleteItem](API_DeleteItem_v20111205.md) 
+  [GetItem](API_GetItem_v20111205.md) 
+  [BatchGetItem](API_BatchGetItem_v20111205.md) 