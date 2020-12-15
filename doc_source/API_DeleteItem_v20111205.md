# DeleteItem<a name="API_DeleteItem_v20111205"></a>

**Important**  
***This section refers to API version 2011\-12\-05, which is deprecated and should not be used for new applications\.***  
 **For documentation on the current low\-level API, see the [Amazon DynamoDB API Reference](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/)\.**

## Description<a name="API_DeleteItem_Description"></a>

Deletes a single item in a table by primary key\. You can perform a conditional delete operation that deletes the item if it exists, or if it has an expected attribute value\.

**Note**  
If you specify `DeleteItem` without attributes or values, all the attributes for the item are deleted\.   
Unless you specify conditions, the `DeleteItem` is an idempotent operation; running it multiple times on the same item or attribute does *not* result in an error response\.  
Conditional deletes are useful for only deleting items and attributes if specific conditions are met\. If the conditions are met, DynamoDB performs the delete\. Otherwise, the item is not deleted\.   
You can perform the expected conditional check on one attribute per operation\.

## Requests<a name="API_DeleteItem_RequestParameters"></a>

### Syntax<a name="API_DeleteItem_RequestParameters.syntax"></a>

```
// This header is abbreviated. 
// For a sample of a complete header, see DynamoDB Low\-Level API.
POST / HTTP/1.1 
x-amz-target: DynamoDB_20111205.DeleteItem 
content-type: application/x-amz-json-1.0 

{"TableName":"Table1",
    "Key":
        {"HashKeyElement":{"S":"AttributeValue1"},"RangeKeyElement":{"N":"AttributeValue2"}},
    "Expected":{"AttributeName3":{"Value":{"S":"AttributeValue3"}}},
    "ReturnValues":"ALL_OLD"}
}
```


****  

|  Name  |  Description  |  Required | 
| --- | --- | --- | 
|  TableName  |  The name of the table containing the item to delete\. Type: String  |  Yes  | 
|  Key  | The primary key that defines the item\. For more information about primary keys, see [Primary Key](HowItWorks.CoreComponents.md#HowItWorks.CoreComponents.PrimaryKey)\.Type: Map of `HashKeyElement` to its value and `RangeKeyElement` to its value\. | Yes | 
| Expected  | Designates an attribute for a conditional delete\. The `Expected` parameter allows you to provide an attribute name, and whether or not DynamoDB should check to see if the attribute has a particular value before deleting it\.Type: Map of attribute names\. | No | 
| Expected:AttributeName  | The name of the attribute for the conditional put\. Type: String | No | 
| Expected:AttributeName: ExpectedAttributeValue | Use this parameter to specify whether or not a value already exists for the attribute name\-value pair\. The following JSON notation deletes the item if the "Color" attribute doesn't exist for that item:<pre>"Expected" :<br />	{"Color":{"Exists":false}}</pre>The following JSON notation checks to see if the attribute with name "Color" has an existing value of "Yellow" before deleting the item: <pre>"Expected" : <br />	{"Color":{"Exists":true},{"Value":{"S":"Yellow"}}}</pre>By default, if you use the `Expected` parameter and provide a `Value`, DynamoDB assumes the attribute exists and has a current value to be replaced\. So you don't have to specify `{"Exists":true}`, because it is implied\. You can shorten the request to:<pre>"Expected" : <br />	{"Color":{"Value":{"S":"Yellow"}}}</pre> If you specify `{"Exists":true}` without an attribute value to check, DynamoDB returns an error\.  | No | 
| ReturnValues  | Use this parameter if you want to get the attribute name\-value pairs before they were deleted\. Possible parameter values are `NONE` \(default\) or `ALL_OLD`\. If `ALL_OLD` is specified, the content of the old item is returned\. If this parameter is not provided or is `NONE`, nothing is returned\.Type: String | No | 

## Responses<a name="API_DeleteItem_CommonResponseElements"></a>

### Syntax<a name="API_DeleteItem_CommonResponseElements.syntax"></a>

```
HTTP/1.1 200 OK
x-amzn-RequestId: CSOC7TJPLR0OOKIRLGOHVAICUFVV4KQNSO5AEMVJF66Q9ASUAAJG
content-type: application/x-amz-json-1.0
content-length: 353
Date: Tue, 12 Jul 2011 21:31:03 GMT

{"Attributes":
    {"AttributeName3":{"SS":["AttributeValue3","AttributeValue4","AttributeValue5"]},
    "AttributeName2":{"S":"AttributeValue2"},
    "AttributeName1":{"N":"AttributeValue1"}
    },
"ConsumedCapacityUnits":1
}
```


****  

|  Name  |  Description  | 
| --- | --- | 
|  Attributes  | If the `ReturnValues` parameter is provided as `ALL_OLD` in the request, DynamoDB returns an array of attribute name\-value pairs \(essentially, the deleted item\)\. Otherwise, the response contains an empty set\. Type: Array of attribute name\-value pairs\. | 
| ConsumedCapacityUnits | The number of write capacity units consumed by the operation\. This value shows the number applied toward your provisioned throughput\. Delete requests on non\-existent items consume 1 write capacity unit\. For more information see [Managing Settings on DynamoDB Provisioned Capacity Tables](ProvisionedThroughput.md)\. Type: Number | 

## Special Errors<a name="API_DeleteItem_SpecialErrors"></a>


****  

|  Error  |  Description  | 
| --- | --- | 
|  ConditionalCheckFailedException  | Conditional check failed\. An expected attribute value was not found\.  | 

## Examples<a name="API_DeleteItem_Examples"></a>

### Sample Request<a name="API_DeleteItem_Examples_Request"></a>

```
// This header is abbreviated. 
// For a sample of a complete header, see DynamoDB Low\-Level API.
POST / HTTP/1.1 
x-amz-target: DynamoDB_20111205.DeleteItem 
content-type: application/x-amz-json-1.0

{"TableName":"comp-table",
    "Key":
        {"HashKeyElement":{"S":"Mingus"},"RangeKeyElement":{"N":"200"}},
    "Expected":
        {"status":{"Value":{"S":"shopping"}}},
    "ReturnValues":"ALL_OLD"
}
```

### Sample Response<a name="API_DeleteItem_Examples_Response"></a>

```
HTTP/1.1 200 OK
x-amzn-RequestId: U9809LI6BBFJA5N2R0TB0P017JVV4KQNSO5AEMVJF66Q9ASUAAJG
content-type: application/x-amz-json-1.0
content-length: 353
Date: Tue, 12 Jul 2011 22:31:23 GMT

{"Attributes":
    {"friends":{"SS":["Dooley","Ben","Daisy"]},
    "status":{"S":"shopping"},
    "time":{"N":"200"},
    "user":{"S":"Mingus"}
    },
"ConsumedCapacityUnits":1
}
```

## Related Actions<a name="API_DeleteItem_Related_Actions"></a>
+  [PutItem](API_PutItem_v20111205.md) 