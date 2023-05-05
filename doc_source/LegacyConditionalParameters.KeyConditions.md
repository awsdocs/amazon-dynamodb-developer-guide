# KeyConditions<a name="LegacyConditionalParameters.KeyConditions"></a>

**Note**  
With the introduction of expression parameters, several older parameters have been deprecated\. New applications should not use these legacy parameters, but should use expression parameters instead\. For more information, see [Using expressions in DynamoDB](Expressions.md)\.

`KeyConditions` are the selection criteria for a `Query` operation\. For a query on a table, you can have conditions only on the table primary key attributes\. You must provide the partition key name and value as an `EQ` condition\. You can optionally provide a second condition, referring to the sort key\.

**Note**  
If you don't provide a sort key condition, all of the items that match the partition key will be retrieved\. If a `FilterExpression` or `QueryFilter` is present, it will be applied after the items are retrieved\.

For a query on an index, you can have conditions only on the index key attributes\. You must provide the index partition key name and value as an `EQ` condition\. You can optionally provide a second condition, referring to the index sort key\.

Each `KeyConditions` element consists of an attribute name to compare, along with the following:
+  `AttributeValueList` \- One or more values to evaluate against the supplied attribute\. The number of values in the list depends on the `ComparisonOperator` being used\.

  For type Number, value comparisons are numeric\.

  String value comparisons for greater than, equals, or less than are based on Unicode with UTF\-8 binary encoding\. For example, `a` is greater than `A`, and `a` is greater than `B`\.

  For Binary, DynamoDB treats each byte of the binary data as unsigned when it compares binary values\.
+  `ComparisonOperator` \- A comparator for evaluating attributes, for example, equals, greater than, less than, and so on\.

  For `KeyConditions`, only the following comparison operators are supported:

   `EQ | LE | LT | GE | GT | BEGINS_WITH | BETWEEN` 

  The following are descriptions of these comparison operators\.
  +  `EQ` : Equal\. 

     `AttributeValueList` can contain only one `AttributeValue` of type String, Number, or Binary \(not a set type\)\. If an item contains an `AttributeValue` element of a different type than the one specified in the request, the value does not match\. For example, `{"S":"6"}` does not equal `{"N":"6"}`\. Also, `{"N":"6"}` does not equal `{"NS":["6", "2", "1"]}`\.
  +  `LE` : Less than or equal\. 

     `AttributeValueList` can contain only one `AttributeValue` element of type String, Number, or Binary \(not a set type\)\. If an item contains an `AttributeValue` element of a different type than the one provided in the request, the value does not match\. For example, `{"S":"6"}` does not equal `{"N":"6"}`\. Also, `{"N":"6"}` does not compare to `{"NS":["6", "2", "1"]}`\.
  +  `LT` : Less than\. 

     `AttributeValueList` can contain only one `AttributeValue` of type String, Number, or Binary \(not a set type\)\. If an item contains an `AttributeValue` element of a different type than the one provided in the request, the value does not match\. For example, `{"S":"6"}` does not equal `{"N":"6"}`\. Also, `{"N":"6"}` does not compare to `{"NS":["6", "2", "1"]}`\.
  +  `GE` : Greater than or equal\. 

     `AttributeValueList` can contain only one `AttributeValue` element of type String, Number, or Binary \(not a set type\)\. If an item contains an `AttributeValue` element of a different type than the one provided in the request, the value does not match\. For example, `{"S":"6"}` does not equal `{"N":"6"}`\. Also, `{"N":"6"}` does not compare to `{"NS":["6", "2", "1"]}`\.
  +  `GT` : Greater than\. 

     `AttributeValueList` can contain only one `AttributeValue` element of type String, Number, or Binary \(not a set type\)\. If an item contains an `AttributeValue` element of a different type than the one provided in the request, the value does not match\. For example, `{"S":"6"}` does not equal `{"N":"6"}`\. Also, `{"N":"6"}` does not compare to `{"NS":["6", "2", "1"]}`\.
  +  `BEGINS_WITH` : Checks for a prefix\. 

     `AttributeValueList` can contain only one `AttributeValue` of type String or Binary \(not a Number or a set type\)\. The target attribute of the comparison must be of type String or Binary \(not a Number or a set type\)\.
  +  `BETWEEN` : Greater than or equal to the first value, and less than or equal to the second value\. 

     `AttributeValueList` must contain two `AttributeValue` elements of the same type, either String, Number, or Binary \(not a set type\)\. A target attribute matches if the target value is greater than, or equal to, the first element and less than, or equal to, the second element\. If an item contains an `AttributeValue` element of a different type than the one provided in the request, the value does not match\. For example, `{"S":"6"}` does not compare to `{"N":"6"}`\. Also, `{"N":"6"}` does not compare to `{"NS":["6", "2", "1"]}` 

## Use *KeyConditionExpression* instead<a name="w120aac41c23c23c15"></a>

Suppose you wanted to retrieve several items with the same partition key from the *Music* table\. You could use a `Query` request with a `KeyConditions` parameter, as in this AWS CLI example:

```
aws dynamodb query \
    --table-name Music \
    --key-conditions '{
        "Artist":{
            "ComparisonOperator":"EQ",
            "AttributeValueList": [ {"S": "No One You Know"} ]
        },
        "SongTitle":{
            "ComparisonOperator":"BETWEEN",
            "AttributeValueList": [ {"S": "A"}, {"S": "M"} ]
        }
    }'
```

But you could use a `KeyConditionExpression` instead:

```
aws dynamodb query \
    --table-name Music \
    --key-condition-expression 'Artist = :a AND SongTitle BETWEEN :t1 AND :t2' \
    --expression-attribute-values '{
        ":a": {"S": "No One You Know"}, 
        ":t1": {"S": "A"}, 
        ":t2": {"S": "M"}
    }'
```