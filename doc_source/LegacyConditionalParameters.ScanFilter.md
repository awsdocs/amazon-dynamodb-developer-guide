# ScanFilter<a name="LegacyConditionalParameters.ScanFilter"></a>

In a `Scan` operation, `ScanFilter` is a condition that evaluates the scan results and returns only the desired values\.

**Note**  
This parameter does not support attributes of type List or Map\.

If you specify more than one condition in the `ScanFilter` map, then by default all of the conditions must evaluate to true\. In other words, the conditions are ANDed together\. \(You can use the [ConditionalOperator](LegacyConditionalParameters.ConditionalOperator.md) parameter to OR the conditions instead\. If you do this, then at least one of the conditions must evaluate to true, rather than all of them\.\)

Each `ScanFilter` element consists of an attribute name to compare, along with the following:
+  `AttributeValueList` \- One or more values to evaluate against the supplied attribute\. The number of values in the list depends on the operator specified in `ComparisonOperator` \.

  For type Number, value comparisons are numeric\.

  String value comparisons for greater than, equals, or less than are based on UTF\-8 binary encoding\. For example, `a` is greater than `A`, and `a` is greater than `B`\.

  For Binary, DynamoDB treats each byte of the binary data as unsigned when it compares binary values\.

  For information on specifying data types in JSON, see [DynamoDB Low\-Level API](Programming.LowLevelAPI.md)\.
+  `ComparisonOperator` \- A comparator for evaluating attributes\. For example, equals, greater than, less than, etc\.

  The following comparison operators are available:

   `EQ | NE | LE | LT | GE | GT | NOT_NULL | NULL | CONTAINS | NOT_CONTAINS | BEGINS_WITH | IN | BETWEEN` 

## Use *FilterExpression* Instead<a name="w615aac41c23c29c13"></a>

Suppose you wanted to scan the *Music* table and apply a condition to the matching items\. You could use a `Scan` request with a `ScanFilter` parameter, as in this AWS CLI example:

```
aws dynamodb scan \
    --table-name Music \
    --scan-filter '{
        "Genre":{
            "AttributeValueList":[ {"S":"Rock"} ],
            "ComparisonOperator": "EQ"
        }
    }'
```

But you could use a `FilterExpression` instead:

```
aws dynamodb scan \
    --table-name Music \
    --filter-expression 'Genre = :g' \
    --expression-attribute-values '{
        ":g": {"S":"Rock"} 
    }'
```