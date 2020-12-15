# Using the CONTAINS Function with PartiQL for DynamoDB<a name="ql-functions.contains"></a>

Returns `TRUE` if the attribute specified by the path is one of the following:
+ A String that contains a particular substring\. 
+ A Set that contains a particular element within the set\.

For more information, see the DynamoDB [contains](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.OperatorsAndFunctions.html#Expressions.OperatorsAndFunctions.Functions) function\. 

## Syntax<a name="ql-functions.contains.syntax"></a>

```
contains( path, substring )
```

## Arguments<a name="ql-functions.contains.arguments"></a>

*path*  
\(Required\) The attribute name or document path to use\.

*substring*  
\(Required\) The attribute substring or set member to check for\. For more information, see the DynamoDB [contains](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.OperatorsAndFunctions.html#Expressions.OperatorsAndFunctions.Functions) function\.

## Return type<a name="ql-functions.contains.return-type"></a>

`bool`

## Examples<a name="ql-functions.contains.examples"></a>

```
SELECT * FROM "Orders" WHERE "OrderID"=1 AND contains("Address", 'Kirkland')
```