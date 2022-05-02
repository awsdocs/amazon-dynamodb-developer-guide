# Expected<a name="LegacyConditionalParameters.Expected"></a>

`Expected` is a conditional block for an `UpdateItem` operation\. `Expected` is a map of attribute/condition pairs\. Each element of the map consists of an attribute name, a comparison operator, and one or more values\. DynamoDB compares the attribute with the value\(s\) you supplied, using the comparison operator\. For each `Expected` element, the result of the evaluation is either true or false\.

If you specify more than one element in the `Expected` map, then by default all of the conditions must evaluate to true\. In other words, the conditions are ANDed together\. \(You can use the `ConditionalOperator` parameter to OR the conditions instead\. If you do this, then at least one of the conditions must evaluate to true, rather than all of them\.\)

If the `Expected` map evaluates to true, then the conditional operation succeeds; otherwise, it fails\.

 `Expected` contains the following:
+  `AttributeValueList` \- One or more values to evaluate against the supplied attribute\. The number of values in the list depends on the `ComparisonOperator` being used\.

  For type Number, value comparisons are numeric\.

  String value comparisons for greater than, equals, or less than are based on Unicode with UTF\-8 binary encoding\. For example, `a` is greater than `A`, and `a` is greater than `B`\.

  For type Binary, DynamoDB treats each byte of the binary data as unsigned when it compares binary values\.
+  `ComparisonOperator` \- A comparator for evaluating attributes in the `AttributeValueList`\. When performing the comparison, DynamoDB uses strongly consistent reads\.

  The following comparison operators are available:

   `EQ | NE | LE | LT | GE | GT | NOT_NULL | NULL | CONTAINS | NOT_CONTAINS | BEGINS_WITH | IN | BETWEEN` 

  The following are descriptions of each comparison operator\.
  +  `EQ` : Equal\. `EQ` is supported for all datatypes, including lists and maps\.

     `AttributeValueList` can contain only one `AttributeValue` element of type String, Number, Binary, String Set, Number Set, or Binary Set\. If an item contains an `AttributeValue` element of a different type than the one provided in the request, the value does not match\. For example, `{"S":"6"}` does not equal `{"N":"6"}`\. Also, `{"N":"6"}` does not equal `{"NS":["6", "2", "1"]}`\.
  +  `NE` : Not equal\. `NE` is supported for all datatypes, including lists and maps\.

     `AttributeValueList` can contain only one `AttributeValue` of type String, Number, Binary, String Set, Number Set, or Binary Set\. If an item contains an `AttributeValue` of a different type than the one provided in the request, the value does not match\. For example, `{"S":"6"}` does not equal `{"N":"6"}`\. Also, `{"N":"6"}` does not equal `{"NS":["6", "2", "1"]}`\.
  +  `LE` : Less than or equal\. 

     `AttributeValueList` can contain only one `AttributeValue` element of type String, Number, or Binary \(not a set type\)\. If an item contains an `AttributeValue` element of a different type than the one provided in the request, the value does not match\. For example, `{"S":"6"}` does not equal `{"N":"6"}`\. Also, `{"N":"6"}` does not compare to `{"NS":["6", "2", "1"]}`\.
  +  `LT` : Less than\. 

     `AttributeValueList` can contain only one `AttributeValue` of type String, Number, or Binary \(not a set type\)\. If an item contains an `AttributeValue` element of a different type than the one provided in the request, the value does not match\. For example, `{"S":"6"}` does not equal `{"N":"6"}`\. Also, `{"N":"6"}` does not compare to `{"NS":["6", "2", "1"]}`\.
  +  `GE` : Greater than or equal\. 

     `AttributeValueList` can contain only one `AttributeValue` element of type String, Number, or Binary \(not a set type\)\. If an item contains an `AttributeValue` element of a different type than the one provided in the request, the value does not match\. For example, `{"S":"6"}` does not equal `{"N":"6"}`\. Also, `{"N":"6"}` does not compare to `{"NS":["6", "2", "1"]}`\.
  +  `GT` : Greater than\. 

     `AttributeValueList` can contain only one `AttributeValue` element of type String, Number, or Binary \(not a set type\)\. If an item contains an `AttributeValue` element of a different type than the one provided in the request, the value does not match\. For example, `{"S":"6"}` does not equal `{"N":"6"}`\. Also, `{"N":"6"}` does not compare to `{"NS":["6", "2", "1"]}`\.
  +  `NOT_NULL` : The attribute exists\. `NOT_NULL` is supported for all datatypes, including lists and maps\.
**Note**  
This operator tests for the existence of an attribute, not its data type\. If the data type of attribute "`a`" is null, and you evaluate it using `NOT_NULL`, the result is a Boolean `true`\. This result is because the attribute "`a`" exists; its data type is not relevant to the `NOT_NULL` comparison operator\.
  +  `NULL` : The attribute does not exist\. `NULL` is supported for all datatypes, including lists and maps\.
**Note**  
This operator tests for the nonexistence of an attribute, not its data type\. If the data type of attribute "`a`" is null, and you evaluate it using `NULL`, the result is a Boolean `false`\. This is because the attribute "`a`" exists; its data type is not relevant to the `NULL` comparison operator\.
  +  `CONTAINS` : Checks for a subsequence, or value in a set\.

     `AttributeValueList` can contain only one `AttributeValue` element of type String, Number, or Binary \(not a set type\)\. If the target attribute of the comparison is of type String, then the operator checks for a substring match\. If the target attribute of the comparison is of type Binary, then the operator looks for a subsequence of the target that matches the input\. If the target attribute of the comparison is a set \("`SS`", "`NS`", or "`BS`"\), then the operator evaluates to true if it finds an exact match with any member of the set\.

    CONTAINS is supported for lists: When evaluating "`a CONTAINS b`", " `a`" can be a list; however, "`b`" cannot be a set, a map, or a list\.
  +  `NOT_CONTAINS` : Checks for absence of a subsequence, or absence of a value in a set\.

     `AttributeValueList` can contain only one `AttributeValue` element of type String, Number, or Binary \(not a set type\)\. If the target attribute of the comparison is a String, then the operator checks for the absence of a substring match\. If the target attribute of the comparison is Binary, then the operator checks for the absence of a subsequence of the target that matches the input\. If the target attribute of the comparison is a set \("`SS`", "`NS`", or "`BS`"\), then the operator evaluates to true if it `does not` find an exact match with any member of the set\.

    NOT\_CONTAINS is supported for lists: When evaluating "`a NOT CONTAINS b`", " `a`" can be a list; however, "`b`" cannot be a set, a map, or a list\.
  +  `BEGINS_WITH` : Checks for a prefix\. 

     `AttributeValueList` can contain only one `AttributeValue` of type String or Binary \(not a Number or a set type\)\. The target attribute of the comparison must be of type String or Binary \(not a Number or a set type\)\.
  +  `IN` : Checks for matching elements within two sets\.

     `AttributeValueList` can contain one or more `AttributeValue` elements of type String, Number, or Binary \(not a set type\)\. These attributes are compared against an existing set type attribute of an item\. If any elements of the input set are present in the item attribute, the expression evaluates to true\.
  +  `BETWEEN` : Greater than or equal to the first value, and less than or equal to the second value\. 

     `AttributeValueList` must contain two `AttributeValue` elements of the same type, either String, Number, or Binary \(not a set type\)\. A target attribute matches if the target value is greater than, or equal to, the first element and less than, or equal to, the second element\. If an item contains an `AttributeValue` element of a different type than the one provided in the request, the value does not match\. For example, `{"S":"6"}` does not compare to `{"N":"6"}`\. Also, `{"N":"6"}` does not compare to `{"NS":["6", "2", "1"]}` 

The following parameters can be used instead of `AttributeValueList` and `ComparisonOperator`:
+  `Value` \- A value for DynamoDB to compare with an attribute\.
+  `Exists` \- A Boolean value that causes DynamoDB to evaluate the value before attempting the conditional operation:
  + If `Exists` is `true`, DynamoDB will check to see if that attribute value already exists in the table\. If it is found, then the condition evaluates to true; otherwise the condition evaluate to false\.
  + If `Exists` is `false`, DynamoDB assumes that the attribute value does `not` exist in the table\. If in fact the value does not exist, then the assumption is valid and the condition evaluates to true\. If the value is found, despite the assumption that it does not exist, the condition evaluates to false\.

  Note that the default value for `Exists` is `true`\.

The `Value` and `Exists` parameters are incompatible with `AttributeValueList` and `ComparisonOperator`\. Note that if you use both sets of parameters at once, DynamoDB will return a `ValidationException` exception\.

**Note**  
This parameter does not support attributes of type List or Map\.

## Use *ConditionExpression* Instead<a name="w615aac41c23c23c21"></a>

Suppose you wanted to modify an item in the *Music* table, but only if a certain condition was true\. You could use an `UpdateItem` request with an `Expected` parameter, as in this AWS CLI example:

```
aws dynamodb update-item \
    --table-name Music \
    --key '{
        "Artist": {"S":"No One You Know"},
        "SongTitle": {"S":"Call Me Today"} 
    }' \
    --attribute-updates '{
        "Price": {
            "Action": "PUT", 
            "Value": {"N":"1.98"}
        }
    }' \
    --expected '{
        "Price": {
            "ComparisonOperator": "LE", 
            "AttributeValueList": [ {"N":"2.00"} ]
        }
    }'
```

But you could use a `ConditionExpression` instead:

```
aws dynamodb update-item \
    --table-name Music \
    --key '{
        "Artist": {"S":"No One You Know"},
        "SongTitle": {"S":"Call Me Today"} 
    }' \
    --update-expression 'SET Price = :p1' \
    --condition-expression 'Price <= :p2' \
    --expression-attribute-values '{
        ":p1": {"N":"1.98"},
        ":p2": {"N":"2.00"}
    }'
```