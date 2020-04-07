# Using Expressions in DynamoDB<a name="Expressions"></a>

In Amazon DynamoDB, you use *expressions* to denote the attributes that you want to read from an item\. You also use expressions when writing an item to indicate any conditions that must be met \(also known as a conditional update\), and to indicate how the attributes are to be updated\. This section describes the basic expression grammar and the available kinds of expressions\.

**Note**  
For backward compatibility, DynamoDB also supports conditional parameters that do not use expressions\. For more information, see [Legacy Conditional Parameters](LegacyConditionalParameters.md)\.  
New applications should use expressions rather than the legacy parameters\.

**Topics**
+ [Specifying Item Attributes When Using Expressions](Expressions.Attributes.md)
+ [Projection Expressions](Expressions.ProjectionExpressions.md)
+ [Expression Attribute Names in DynamoDB](Expressions.ExpressionAttributeNames.md)
+ [Expression Attribute Values](Expressions.ExpressionAttributeValues.md)
+ [Condition Expressions](Expressions.ConditionExpressions.md)
+ [Update Expressions](Expressions.UpdateExpressions.md)