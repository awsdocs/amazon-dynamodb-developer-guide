# Query<a name="API_Query_v20111205"></a>

**Important**  
***This section refers to API version 2011\-12\-05, which is deprecated and should not be used for new applications\.***  
 **For documentation on the current low\-level API, see the [Amazon DynamoDB API Reference](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/)\.**

## Description<a name="API_Query_Description"></a>

A `Query` operation gets the values of one or more items and their attributes by primary key \(`Query` is only available for hash\-and\-range primary key tables\)\. You must provide a specific `HashKeyValue`, and can narrow the scope of the query using comparison operators on the `RangeKeyValue` of the primary key\. Use the `ScanIndexForward` parameter to get results in forward or reverse order by range key\. 

Queries that do not return results consume the minimum read capacity units according to the type of read\.

**Note**  
If the total number of items meeting the query parameters exceeds the 1MB limit, the query stops and results are returned to the user with a `LastEvaluatedKey` to continue the query in a subsequent operation\. Unlike a Scan operation, a Query operation never returns an empty result set *and* a `LastEvaluatedKey`\. The `LastEvaluatedKey` is only provided if the results exceed 1MB, or if you have used the `Limit` parameter\.   
The result can be set for a consistent read using the `ConsistentRead` parameter\.

## Requests<a name="API_Query_RequestParameters"></a>

### Syntax<a name="API_Query_RequestParameters.syntax"></a>

```
// This header is abbreviated.
// For a sample of a complete header, see DynamoDB Low\-Level API.
POST / HTTP/1.1 
x-amz-target: DynamoDB_20111205.Query  
content-type: application/x-amz-json-1.0

{"TableName":"Table1",
	"Limit":2,
	"ConsistentRead":true,
	"HashKeyValue":{"S":"AttributeValue1":},
	"RangeKeyCondition": {"AttributeValueList":[{"N":"AttributeValue2"}],"ComparisonOperator":"GT"}
	"ScanIndexForward":true,
	"ExclusiveStartKey":{
		"HashKeyElement":{"S":"AttributeName1"},
		"RangeKeyElement":{"N":"AttributeName2"}
	},
    "AttributesToGet":["AttributeName1", "AttributeName2", "AttributeName3"]},
}
```


****  

|  Name  |  Description  |  Required | 
| --- | --- | --- | 
|  TableName  |  The name of the table containing the requested items\.  Type: String  |  Yes  | 
| AttributesToGet  | Array of Attribute names\. If attribute names are not specified then all attributes will be returned\. If some attributes are not found, they will not appear in the result\.Type: Array | No | 
| Limit  | The maximum number of items to return \(not necessarily the number of matching items\)\. If DynamoDB processes the number of items up to the limit while querying the table, it stops the query and returns the matching values up to that point, and a `LastEvaluatedKey` to apply in a subsequent operation to continue the query\. Also, if the result set size exceeds 1MB before DynamoDB hits this limit, it stops the query and returns the matching values, and a `LastEvaluatedKey` to apply in a subsequent operation to continue the query\.Type: Number | No | 
| ConsistentRead  | If set to `true`, then a consistent read is issued, otherwise eventually consistent is used\.Type: Boolean | No | 
| Count  | If set to `true`, DynamoDB returns a total number of items that match the query parameters, instead of a list of the matching items and their attributes\. You can apply the `Limit` parameter to count\-only queries\.  Do not set `Count` to `true` while providing a list of `AttributesToGet`; otherwise, DynamoDB returns a validation error\. For more information, see [Counting the Items in the Results](Query.md#Query.Count)\.Type: Boolean | No | 
| HashKeyValue  | Attribute value of the hash component of the composite primary key\.Type: String, Number, or Binary | Yes | 
| RangeKeyCondition  | A container for the attribute values and comparison operators to use for the query\. A query request does not require a `RangeKeyCondition`\. If you provide only the `HashKeyValue`, DynamoDB returns all items with the specified hash key element value\.Type: Map | No | 
| RangeKeyCondition:​ AttributeValueList | The attribute values to evaluate for the query parameters\. The `AttributeValueList` contains one attribute value, unless a `BETWEEN` comparison is specified\. For the `BETWEEN` comparison, the `AttributeValueList` contains two attribute values\. Type: A map of `AttributeValue` to a `ComparisonOperator`\.  | No | 
| RangeKeyCondition:​ ComparisonOperator |  The criteria for evaluating the provided attributes, such as equals, greater\-than, etc\. The following are valid comparison operators for a Query operation\.  String value comparisons for greater than, equals, or less than are based on ASCII character code values\. For example, `a` is greater than `A`, and `aa` is greater than `B`\. For a list of code values, see [http://en\.wikipedia\.org/wiki/ASCII\#ASCII\_printable\_characters](http://en.wikipedia.org/wiki/ASCII#ASCII_printable_characters)\.  For Binary, DynamoDB treats each byte of the binary data as unsigned when it compares binary values, for example when evaluating query expressions\.   Type: String or Binary  | No | 
|   | `EQ` : Equal\. For `EQ`, `AttributeValueList` can contain only one `AttributeValue` of type String, Number, or Binary \(not a set\)\. If an item contains an `AttributeValue` of a different type than the one specified in the request, the value does not match\. For example, `{"S":"6"}` does not equal `{"N":"6"}`\. Also, `{"N":"6"}` does not equal `{"NS":["6", "2", "1"]}`\. |   | 
|   | `LE` : Less than or equal\. For `LE`, `AttributeValueList` can contain only one `AttributeValue` of type String, Number, or Binary \(not a set\)\. If an item contains an `AttributeValue` of a different type than the one specified in the request, the value does not match\. For example, `{"S":"6"}` does not equal `{"N":"6"}`\. Also, `{"N":"6"}` does not compare to `{"NS":["6", "2", "1"]}`\. |   | 
|   | `LT` : Less than\. For `LT`, `AttributeValueList` can contain only one `AttributeValue` of type String, Number, or Binary \(not a set\)\. If an item contains an `AttributeValue` of a different type than the one specified in the request, the value does not match\. For example, `{"S":"6"}` does not equal `{"N":"6"}`\. Also, `{"N":"6"}` does not compare to `{"NS":["6", "2", "1"]}`\. |   | 
|   | `GE` : Greater than or equal\. For `GE`, `AttributeValueList` can contain only one `AttributeValue` of type String, Number, or Binary \(not a set\)\. If an item contains an `AttributeValue` of a different type than the one specified in the request, the value does not match\. For example, `{"S":"6"}` does not equal `{"N":"6"}`\. Also, `{"N":"6"}` does not compare to `{"NS":["6", "2", "1"]}`\. |   | 
|   | `GT` : Greater than\. For `GT`, `AttributeValueList` can contain only one `AttributeValue` of type String, Number, or Binary \(not a set\)\. If an item contains an `AttributeValue` of a different type than the one specified in the request, the value does not match\. For example, `{"S":"6"}` does not equal `{"N":"6"}`\. Also, `{"N":"6"}` does not compare to `{"NS":["6", "2", "1"]}`\. |   | 
|   | `BEGINS_WITH` : checks for a prefix\. For `BEGINS_WITH`, `AttributeValueList` can contain only one `AttributeValue` of type String or Binary \(not a Number or a set\)\. The target attribute of the comparison must be a String or Binary \(not a Number or a set\)\. |   | 
|   | `BETWEEN` : Greater than, or equal to, the first value and less than, or equal to, the second value\. For `BETWEEN`, `AttributeValueList` must contain two `AttributeValue` elements of the same type, either String, Number, or Binary \(not a set\)\. A target attribute matches if the target value is greater than, or equal to, the first element and less than, or equal to, the second element\. If an item contains an `AttributeValue` of a different type than the one specified in the request, the value does not match\. For example, `{"S":"6"}` does not compare to `{"N":"6"}`\. Also, `{"N":"6"}` does not compare to `{"NS":["6", "2", "1"]}`\. |   | 
| ScanIndexForward | Specifies ascending or descending traversal of the index\. DynamoDB returns results reflecting the requested order determined by the range key: If the data type is Number, the results are returned in numeric order; otherwise, the traversal is based on ASCII character code values\.Type: BooleanDefault is `true` \(ascending\)\. | No | 
| ExclusiveStartKey | Primary key of the item from which to continue an earlier query\. An earlier query might provide this value as the `LastEvaluatedKey` if that query operation was interrupted before completing the query; either because of the result set size or the `Limit` parameter\. The `LastEvaluatedKey` can be passed back in a new query request to continue the operation from that point\.Type: `HashKeyElement`, or `HashKeyElement` and `RangeKeyElement` for a composite primary key\. | No | 

## Responses<a name="API_Query_ResponseElements"></a>

### Syntax<a name="API_Query_ResponseElements.syntax"></a>

```
HTTP/1.1 200 
x-amzn-RequestId: 8966d095-71e9-11e0-a498-71d736f27375 
content-type: application/x-amz-json-1.0
content-length: 308

{"Count":2,"Items":[{
    "AttributeName1":{"S":"AttributeValue1"},
    "AttributeName2":{"N":"AttributeValue2"},
    "AttributeName3":{"S":"AttributeValue3"}
    },{
    "AttributeName1":{"S":"AttributeValue3"},
    "AttributeName2":{"N":"AttributeValue4"},
    "AttributeName3":{"S":"AttributeValue3"},
    "AttributeName5":{"B":"dmFsdWU="}
}],
    "LastEvaluatedKey":{"HashKeyElement":{"AttributeValue3":"S"},
                        "RangeKeyElement":{"AttributeValue4":"N"}
     },
     "ConsumedCapacityUnits":1
}
```


****  

|  Name  |  Description  | 
| --- | --- | 
| Items  | Item attributes meeting the query parameters\.Type: Map of attribute names to and their data types and values\. | 
| Count  |  Number of items in the response\. For more information, see [Counting the Items in the Results](Query.md#Query.Count)\. Type: Number  | 
| LastEvaluatedKey | Primary key of the item where the query operation stopped, inclusive of the previous result set\. Use this value to start a new operation excluding this value in the new request\.The `LastEvaluatedKey` is `null` when the entire query result set is complete \(i\.e\. the operation processed the “last page”\)\. Type: `HashKeyElement`, or `HashKeyElement` and `RangeKeyElement` for a composite primary key\. | 
| ConsumedCapacityUnits | The number of read capacity units consumed by the operation\. This value shows the number applied toward your provisioned throughput\. For more information see [Managing Settings on DynamoDB Provisioned Capacity Tables](ProvisionedThroughput.md)\. Type: Number | 

## Special Errors<a name="API_Query_SpecialErrors"></a>


****  

|  Error  |  Description  | 
| --- | --- | 
| ResourceNotFoundException  | The specified table was not found\. | 

## Examples<a name="API_Query_Examples"></a>

 For examples using the AWS SDK, see [Working with Queries in DynamoDB](Query.md)\.

### Sample Request<a name="API_Query_Examples_Request"></a>

```
// This header is abbreviated. For a sample of a complete header, see DynamoDB Low\-Level API.
POST / HTTP/1.1 
x-amz-target: DynamoDB_20111205.Query  
content-type: application/x-amz-json-1.0

{"TableName":"1-hash-rangetable",
	"Limit":2,
	"HashKeyValue":{"S":"John"},
	"ScanIndexForward":false,
	"ExclusiveStartKey":{
		"HashKeyElement":{"S":"John"},
		"RangeKeyElement":{"S":"The Matrix"}
	}
}
```

### Sample Response<a name="API_Query_Examples_Response"></a>

```
HTTP/1.1 200 
x-amzn-RequestId: 3647e778-71eb-11e0-a498-71d736f27375 
content-type: application/x-amz-json-1.0
content-length: 308

{"Count":2,"Items":[{
	"fans":{"SS":["Jody","Jake"]},
	"name":{"S":"John"},
	"rating":{"S":"***"},
	"title":{"S":"The End"}
	},{
	"fans":{"SS":["Jody","Jake"]},
	"name":{"S":"John"},
	"rating":{"S":"***"},
	"title":{"S":"The Beatles"}
	}],
	"LastEvaluatedKey":{"HashKeyElement":{"S":"John"},"RangeKeyElement":{"S":"The Beatles"}},
"ConsumedCapacityUnits":1
}
```

### Sample Request<a name="API_Query_Examples_Request2"></a>

```
// This header is abbreviated. For a sample of a complete header, see DynamoDB Low\-Level API.
POST / HTTP/1.1 
x-amz-target: DynamoDB_20111205.Query 
content-type: application/x-amz-json-1.0
 
{"TableName":"1-hash-rangetable",
	"Limit":2,
	"HashKeyValue":{"S":"Airplane"},
	"RangeKeyCondition":{"AttributeValueList":[{"N":"1980"}],"ComparisonOperator":"EQ"},
	"ScanIndexForward":false}
```

### Sample Response<a name="API_Query_Examples_Response2"></a>

```
HTTP/1.1 200 
x-amzn-RequestId: 8b9ee1ad-774c-11e0-9172-d954e38f553a
content-type: application/x-amz-json-1.0
content-length: 119 
 
{"Count":1,"Items":[{
	"fans":{"SS":["Dave","Aaron"]},
	"name":{"S":"Airplane"},
	"rating":{"S":"***"},
	"year":{"N":"1980"}
	}],
"ConsumedCapacityUnits":1
}
```

## Related Actions<a name="API_Query_Related_Actions"></a>
+  [Scan](API_Scan_v20111205.md) 