# ConditionalOperator<a name="LegacyConditionalParameters.ConditionalOperator"></a>

**Note**  
With the introduction of expression parameters, several older parameters have been deprecated\. New applications should not use these legacy parameters, but should use expression parameters instead\. For more information, see [Using expressions in DynamoDB](Expressions.md)\.

A logical operator to apply to the conditions in a `Expected`, `QueryFilter` or `ScanFilter` map:
+ AND \- If all of the conditions evaluate to true, then the entire map evaluates to true\.
+ OR \- If at least one of the conditions evaluate to true, then the entire map evaluates to true\.

If you omit `ConditionalOperator`, then `AND` is the default\.

The operation will succeed only if the entire map evaluates to true\.

**Note**  
This parameter does not support attributes of type List or Map\.