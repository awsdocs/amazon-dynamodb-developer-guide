# Using expressions in DynamoDB<a name="Expressions"></a>

In Amazon DynamoDB, you use *expressions* to denote the attributes that you want to read from an item\. You also use expressions when writing an item to indicate any conditions that must be met \(also known as a conditional update\), and to indicate how the attributes are to be updated\. This section describes the basic expression grammar and the available kinds of expressions\.

**Note**  
For backward compatibility, DynamoDB also supports conditional parameters that do not use expressions\. For more information, see [Legacy conditional parameters](LegacyConditionalParameters.md)\.  
New applications should use expressions rather than the legacy parameters\.

**Topics**
+ [Specifying item attributes when using expressions](Expressions.Attributes.md)
+ [Projection expressions](Expressions.ProjectionExpressions.md)
+ [Expression attribute names in DynamoDB](Expressions.ExpressionAttributeNames.md)
+ [Expression attribute values](Expressions.ExpressionAttributeValues.md)
+ [Condition expressions](Expressions.ConditionExpressions.md)
+ [Update expressions](Expressions.UpdateExpressions.md)