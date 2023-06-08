# Comparison operator and function reference<a name="Expressions.OperatorsAndFunctions"></a>

This section covers the built\-in functions and keywords for writing filter expressions and condition expressions in Amazon DynamoDB\.

**Topics**
+ [Syntax for filter and condition expressions](#Expressions.OperatorsAndFunctions.Syntax)
+ [Making comparisons](#Expressions.OperatorsAndFunctions.Comparators)
+ [Functions](#Expressions.OperatorsAndFunctions.Functions)
+ [Logical evaluations](#Expressions.OperatorsAndFunctions.LogicalEvaluations)
+ [Parentheses](#Expressions.OperatorsAndFunctions.Parentheses)
+ [Precedence in conditions](#Expressions.OperatorsAndFunctions.Precedence)

## Syntax for filter and condition expressions<a name="Expressions.OperatorsAndFunctions.Syntax"></a>

In the following syntax summary, an *operand* can be the following:
+ A top\-level attribute name, such as `Id`, `Title`, `Description`, or `ProductCategory`
+ A document path that references a nested attribute

```
condition-expression ::=
      operand comparator operand
    | operand BETWEEN operand AND operand
    | operand IN ( operand (',' operand (, ...) ))
    | function
    | condition AND condition
    | condition OR condition
    | NOT condition
    | ( condition )

comparator ::=
    =
    | <>
    | <
    | <=
    | >
    | >=

function ::=
    attribute_exists (path)
    | attribute_not_exists (path)
    | attribute_type (path, type)
    | begins_with (path, substr)
    | contains (path, operand)
    | size (path)
```

## Making comparisons<a name="Expressions.OperatorsAndFunctions.Comparators"></a>

Use these comparators to compare an operand against a range of values or an enumerated list of values:
+ `a = b` — true if *a* is equal to *b*
+ `a <> b` — true if *a* is not equal to *b*
+ `a < b` — true if *a* is less than *b*
+ `a <= b` — true if *a* is less than or equal to *b*
+ `a > b` — true if *a* is greater than *b*
+ `a >= b` — true if *a* is greater than or equal to *b*

Use the `BETWEEN` and `IN` keywords to compare an operand against a range of values or an enumerated list of values:
+ `a BETWEEN b AND c` \- true if *a* is greater than or equal to *b*, and less than or equal to *c*\.
+ `a IN (b, c, d) ` — true if *a* is equal to any value in the list — for example, any of *b*, *c* or *d*\. The list can contain up to 100 values, separated by commas\.

## Functions<a name="Expressions.OperatorsAndFunctions.Functions"></a>

Use the following functions to determine whether an attribute exists in an item, or to evaluate the value of an attribute\. These function names are case sensitive\. For a nested attribute, you must provide its full document path\.


****  

| Function | Description | 
| --- | --- | 
|  `attribute_exists (path)`  | True if the item contains the attribute specified by `path`\. Example: Check whether an item in the `Product` table has a side view picture\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.OperatorsAndFunctions.html)  | 
|  `attribute_not_exists (path)`  | True if the attribute specified by `path` does not exist in the item\. Example: Check whether an item has a `Manufacturer` attribute\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.OperatorsAndFunctions.html)  | 
|  `attribute_type (path, type)`  |  True if the attribute at the specified path is of a particular data type\. The `type` parameter must be one of the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.OperatorsAndFunctions.html) You must use an expression attribute value for the `type` parameter\. Example: Check whether the `QuantityOnHand` attribute is of type List\. In this example, `:v_sub` is a placeholder for the string `L`\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.OperatorsAndFunctions.html) You must use an expression attribute value for the `type` parameter\.   | 
|  `begins_with (path, substr)`  |  True if the attribute specified by `path` begins with a particular substring\. Example: Check whether the first few characters of the front view picture URL are `http://`\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.OperatorsAndFunctions.html) The expression attribute value `:v_sub` is a placeholder for `http://`\.  | 
|  `contains (path, operand)`  | True if the attribute specified by `path` is one of the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.OperatorsAndFunctions.html) The `operand` must be a `String` if the attribute specified by `path` is a `String`\. If the attribute specified by `path` is a `Set`, the `operand` must be the set's element type\. The path and the operand must be distinct; that is, `contains (a, a)` returns an error\. Example: Check whether the `Brand` attribute contains the substring `Company`\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.OperatorsAndFunctions.html) The expression attribute value `:v_sub` is a placeholder for `Company`\. Example: Check whether the product is available in red\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.OperatorsAndFunctions.html) The expression attribute value `:v_sub` is a placeholder for `Red`\. | 
|  `size (path)`  | Returns a number representing an attribute's size\. The following are valid data types for use with `size`\.  If the attribute is of type `String`, `size` returns the length of the string\. Example: Check whether the string `Brand` is less than or equal to 20 characters\. The expression attribute value `:v_sub` is a placeholder for `20`\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.OperatorsAndFunctions.html)  If the attribute is of type `Binary`, `size` returns the number of bytes in the attribute value\. Example: Suppose that the `ProductCatalog` item has a binary attribute named `VideoClip`, which contains a short video of the product in use\. The following expression checks whether `VideoClip` exceeds 64,000 bytes\. The expression attribute value `:v_sub` is a placeholder for `64000`\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.OperatorsAndFunctions.html)  If the attribute is a `Set` data type, `size` returns the number of elements in the set\.  Example: Check whether the product is available in more than one color\. The expression attribute value `:v_sub` is a placeholder for `1`\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.OperatorsAndFunctions.html)  If the attribute is of type `List` or `Map`, `size` returns the number of child elements\. Example: Check whether the number of `OneStar` reviews has exceeded a certain threshold\. The expression attribute value `:v_sub` is a placeholder for `3`\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.OperatorsAndFunctions.html)  | 

## Logical evaluations<a name="Expressions.OperatorsAndFunctions.LogicalEvaluations"></a>

Use the `AND`, `OR`, and `NOT` keywords to perform logical evaluations\. In the list following, *a* and *b* represent conditions to be evaluated\.
+ `a AND b` — true if *a* and *b* are both true\.
+ `a OR b` — true if either *a* or *b* \(or both\) are true\.
+ `NOT a` — true if *a* is false; false if *a* is true\.

## Parentheses<a name="Expressions.OperatorsAndFunctions.Parentheses"></a>

Use parentheses to change the precedence of a logical evaluation\. For example, suppose that conditions *a* and *b* are true, and that condition *c* is false\. The following expression evaluates to true:
+ `a OR b AND c`

However, if you enclose a condition in parentheses, it is evaluated first\. For example, the following evaluates to false:
+  `(a OR b) AND c`

**Note**  
You can nest parentheses in an expression\. The innermost ones are evaluated first\.

## Precedence in conditions<a name="Expressions.OperatorsAndFunctions.Precedence"></a>

 DynamoDB evaluates conditions from left to right using the following precedence rules:
+ `= <> < <= > >=`
+ `IN`
+ `BETWEEN`
+ `attribute_exists attribute_not_exists begins_with contains`
+ Parentheses
+ `NOT`
+ `AND`
+ `OR`