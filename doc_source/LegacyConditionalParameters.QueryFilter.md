# QueryFilter<a name="LegacyConditionalParameters.QueryFilter"></a>

**Note**  
With the introduction of expression parameters, several older parameters have been deprecated\. New applications should not use these legacy parameters, but should use expression parameters instead\. For more information, see [Using expressions in DynamoDB](Expressions.md)\.

In a `Query` operation, `QueryFilter` is a condition that evaluates the query results after the items are read and returns only the desired values\.

This parameter does not support attributes of type List or Map\.

**Note**  
A `QueryFilter` is applied after the items have already been read; the process of filtering does not consume any additional read capacity units\.

If you provide more than one condition in the `QueryFilter` map, then by default all of the conditions must evaluate to true\. In other words, the conditions are ANDed together\. \(You can use the [ConditionalOperator](LegacyConditionalParameters.ConditionalOperator.md) parameter to OR the conditions instead\. If you do this, then at least one of the conditions must evaluate to true, rather than all of them\.\)

Note that `QueryFilter` does not allow key attributes\. You cannot define a filter condition on a partition key or a sort key\.

Each `QueryFilter` element consists of an attribute name to compare, along with the following:
+  `AttributeValueList` \- One or more values to evaluate against the supplied attribute\. The number of values in the list depends on the operator specified in `ComparisonOperator`\.

  For type Number, value comparisons are numeric\.

  String value comparisons for greater than, equals, or less than are based on UTF\-8 binary encoding\. For example, `a` is greater than `A`, and `a` is greater than `B`\.

  For type Binary, DynamoDB treats each byte of the binary data as unsigned when it compares binary values\.

  For information on specifying data types in JSON, see [DynamoDB low\-level API](Programming.LowLevelAPI.md)\.
+  `ComparisonOperator` \- A comparator for evaluating attributes\. For example, equals, greater than, less than, etc\.

  The following comparison operators are available:

   `EQ | NE | LE | LT | GE | GT | NOT_NULL | NULL | CONTAINS | NOT_CONTAINS | BEGINS_WITH | IN | BETWEEN` 

## Use *FilterExpression* instead<a name="w120aac41c23c25c19"></a>

Suppose you wanted to query the *Music* table and apply a condition to the matching items\. You could use a `Query` request with a `QueryFilter` parameter, as in this AWS CLI example:

```
aws dynamodb query \
    --table-name Music \
    --key-conditions '{
        "Artist": {
            "ComparisonOperator": "EQ",
            "AttributeValueList": [ {"S": "No One You Know"} ]
        }   
    }' \
    --query-filter '{
        "Price": {
            "ComparisonOperator": "GT",
            "AttributeValueList": [ {"N": "1.00"} ]
        }   
    }'
```

But you could use a `FilterExpression` instead:

```
aws dynamodb query \
    --table-name Music \
    --key-condition-expression 'Artist = :a' \
    --filter-expression 'Price > :p' \
    --expression-attribute-values '{
        ":p": {"N":"1.00"}, 
        ":a": {"S":"No One You Know"}
    }'
```