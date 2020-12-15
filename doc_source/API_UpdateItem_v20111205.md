# UpdateItem<a name="API_UpdateItem_v20111205"></a>

**Important**  
***This section refers to API version 2011\-12\-05, which is deprecated and should not be used for new applications\.***  
 **For documentation on the current low\-level API, see the [Amazon DynamoDB API Reference](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/)\.**

## Description<a name="API_UpdateItem_Description"></a>

Edits an existing item's attributes\. You can perform a conditional update \(insert a new attribute name\-value pair if it doesn't exist, or replace an existing name\-value pair if it has certain expected attribute values\)\.

**Note**  
You cannot update the primary key attributes using UpdateItem\. Instead, delete the item and use PutItem to create a new item with new attributes\.

The UpdateItem operation includes an `Action` parameter, which defines how to perform the update\. You can put, delete, or add attribute values\. 

Attribute values may not be null; string and binary type attributes must have lengths greater than zero; and set type attributes must not be empty\. Requests with empty values will be rejected with a `ValidationException`\.

If an existing item has the specified primary key: 
+ **PUT—** Adds the specified attribute\. If the attribute exists, it is replaced by the new value\. 
+ **DELETE—** If no value is specified, this removes the attribute and its value\. If a set of values is specified, then the values in the specified set are removed from the old set\. So if the attribute value contains \[a,b,c\] and the delete action contains \[a,c\], then the final attribute value is \[b\]\. The type of the specified value must match the existing value type\. Specifying an empty set is not valid\.
+ **ADD—** Only use the add action for numbers or if the target attribute is a set \(including string sets\)\. ADD does not work if the target attribute is a single string value or a scalar binary value\. The specified value is added to a numeric value \(incrementing or decrementing the existing numeric value\) or added as an additional value in a string set\. If a set of values is specified, the values are added to the existing set\. For example if the original set is \[1,2\] and supplied value is \[3\], then after the add operation the set is \[1,2,3\], not \[4,5\]\. An error occurs if an Add action is specified for a set attribute and the attribute type specified does not match the existing set type\. 

  If you use ADD for an attribute that does not exist, the attribute and its values are added to the item\.

If no item matches the specified primary key: 
+ **PUT—** Creates a new item with specified primary key\. Then adds the specified attribute\. 
+ **DELETE—** Nothing happens\. 
+ **ADD—** Creates an item with supplied primary key and number \(or set of numbers\) for the attribute value\. Not valid for a string or a binary type\. 

**Note**  
If you use `ADD` to increment or decrement a number value for an item that doesn't exist before the update, DynamoDB uses `0` as the initial value\. Also, if you update an item using `ADD` to increment or decrement a number value for an attribute that doesn't exist before the update \(but the item does\) DynamoDB uses `0` as the initial value\. For example, you use `ADD` to add `+3` to an attribute that did not exist before the update\. DynamoDB uses `0` for the initial value, and the value after the update is `3`\. 

For more information about using this operation, see [Working with Items and Attributes](WorkingWithItems.md)\. 

## Requests<a name="API_UpdateItem_RequestParameters"></a>

### Syntax<a name="API_UpdateItem_RequestParameters.syntax"></a>

```
// This header is abbreviated.
// For a sample of a complete header, see DynamoDB Low\-Level API.
POST / HTTP/1.1 
x-amz-target: DynamoDB_20111205.UpdateItem
content-type: application/x-amz-json-1.0

{"TableName":"Table1",
    "Key":
        {"HashKeyElement":{"S":"AttributeValue1"},
        "RangeKeyElement":{"N":"AttributeValue2"}},
    "AttributeUpdates":{"AttributeName3":{"Value":{"S":"AttributeValue3_New"},"Action":"PUT"}},
    "Expected":{"AttributeName3":{"Value":{"S":"AttributeValue3_Current"}}},
    "ReturnValues":"ReturnValuesConstant"
}
```


****  

|  Name  |  Description  |  Required | 
| --- | --- | --- | 
|  TableName  |  The name of the table containing the item to update\.  Type: String  |  Yes  | 
|  Key  | The primary key that defines the item\. For more information about primary keys, see [Primary Key](HowItWorks.CoreComponents.md#HowItWorks.CoreComponents.PrimaryKey)\.Type: Map of `HashKeyElement` to its value and `RangeKeyElement` to its value\. | Yes | 
| AttributeUpdates | Map of attribute name to the new value and action for the update\. The attribute names specify the attributes to modify, and cannot contain any primary key attributes\.Type: Map of attribute name, value, and an action for the attribute update\. |  | 
| AttributeUpdates:Action | Specifies how to perform the update\. Possible values: `PUT` \(default\), `ADD` or `DELETE`\. The semantics are explained in the UpdateItem description\.Type: StringDefault: `PUT` | No | 
| Expected  | Designates an attribute for a conditional update\. The `Expected` parameter allows you to provide an attribute name, and whether or not DynamoDB should check to see if the attribute value already exists; or if the attribute value exists and has a particular value before changing it\.Type: Map of attribute names\. | No | 
| Expected:AttributeName  | The name of the attribute for the conditional put\.Type: String | No | 
| Expected:AttributeName: ExpectedAttributeValue | Use this parameter to specify whether or not a value already exists for the attribute name\-value pair\. The following JSON notation updates the item if the "Color" attribute doesn't already exist for that item:<pre>"Expected" :<br />	{"Color":{"Exists":false}}</pre>The following JSON notation checks to see if the attribute with name "Color" has an existing value of "Yellow" before updating the item: <pre>"Expected" : <br />	{"Color":{"Exists":true},{"Value":{"S":"Yellow"}}}</pre>By default, if you use the `Expected` parameter and provide a `Value`, DynamoDB assumes the attribute exists and has a current value to be replaced\. So you don't have to specify `{"Exists":true}`, because it is implied\. You can shorten the request to:<pre>"Expected" : <br />	{"Color":{"Value":{"S":"Yellow"}}}</pre> If you specify `{"Exists":true}` without an attribute value to check, DynamoDB returns an error\.  | No | 
| ReturnValues  | Use this parameter if you want to get the attribute name\-value pairs before they were updated with the `UpdateItem` request\. Possible parameter values are `NONE` \(default\) or `ALL_OLD`, `UPDATED_OLD`, `ALL_NEW` or `UPDATED_NEW`\. If `ALL_OLD` is specified, and `UpdateItem` overwrote an attribute name\-value pair, the content of the old item is returned\. If this parameter is not provided or is `NONE`, nothing is returned\. If `ALL_NEW` is specified, then all the attributes of the new version of the item are returned\. If `UPDATED_NEW` is specified, then the new versions of only the updated attributes are returned\.Type: String | No | 

## Responses<a name="API_UpdateItem_CommonResponseElements"></a>

### Syntax<a name="API_UpdateItem_CommonResponseElements.syntax"></a>

The following syntax example assumes the request specified a `ReturnValues` parameter of `ALL_OLD`; otherwise, the response has only the `ConsumedCapacityUnits` element\.

```
HTTP/1.1 200 
x-amzn-RequestId: 8966d095-71e9-11e0-a498-71d736f27375 
content-type: application/x-amz-json-1.0
content-length: 140

{"Attributes":{
	"AttributeName1":{"S":"AttributeValue1"},
	"AttributeName2":{"S":"AttributeValue2"},
	"AttributeName3":{"S":"AttributeValue3"},
	"AttributeName5":{"B":"dmFsdWU="}
	},
"ConsumedCapacityUnits":1
}
```


****  

|  Name  |  Description  | 
| --- | --- | 
|  Attributes  | A map of attribute name\-value pairs, but only if the `ReturnValues` parameter is specified as something other than `NONE` in the request\.Type: Map of attribute name\-value pairs\. | 
| ConsumedCapacityUnits | The number of write capacity units consumed by the operation\. This value shows the number applied toward your provisioned throughput\. For more information see [Managing Settings on DynamoDB Provisioned Capacity Tables](ProvisionedThroughput.md)\. Type: Number | 

## Special Errors<a name="API_UpdateItem_SpecialErrors"></a>


****  

|  Error  |  Description  | 
| --- | --- | 
|  ConditionalCheckFailedException  | Conditional check failed\. Attribute \("\+ name \+"\) value is \("\+ value \+"\) but was expected \("\+ expValue \+"\)  | 
| ResourceNotFoundExceptions  | The specified item or attribute was not found\. | 

## Examples<a name="API_UpdateItem_Examples"></a>

For examples using the AWS SDK, see [Working with Items and Attributes](WorkingWithItems.md)\.

### Sample Request<a name="API_UpdateItem_Examples_Request"></a>

```
// This header is abbreviated. For a sample of a complete header, see DynamoDB Low\-Level API.
POST / HTTP/1.1 
x-amz-target: DynamoDB_20111205.UpdateItem
content-type: application/x-amz-json-1.0 

{"TableName":"comp5",
    "Key":
        {"HashKeyElement":{"S":"Julie"},"RangeKeyElement":{"N":"1307654350"}},
    "AttributeUpdates":
        {"status":{"Value":{"S":"online"},
        "Action":"PUT"}},
    "Expected":{"status":{"Value":{"S":"offline"}}},
    "ReturnValues":"ALL_NEW"
}
```

### Sample Response<a name="API_UpdateItem_Examples_Response"></a>

```
HTTP/1.1 200 OK
x-amzn-RequestId: 5IMHO7F01Q9P7Q6QMKMMI3R3QRVV4KQNSO5AEMVJF66Q9ASUAAJG
content-type: application/x-amz-json-1.0
content-length: 121
Date: Fri, 26 Aug 2011 21:05:00 GMT

{"Attributes":
    {"friends":{"SS":["Lynda, Aaron"]},
    "status":{"S":"online"},
    "time":{"N":"1307654350"},
    "user":{"S":"Julie"}},
"ConsumedCapacityUnits":1
}
```

## Related Actions<a name="API_UpdateItem_Related_Actions"></a>
+  [PutItem](API_PutItem_v20111205.md) 
+  [DeleteItem](API_DeleteItem_v20111205.md) 