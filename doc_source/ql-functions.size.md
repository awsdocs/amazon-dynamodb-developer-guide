# Using the SIZE Function with PartiQL for Amazon DynamoDB<a name="ql-functions.size"></a>

Returns a number representing an attribute's size in bytes\. The following are valid data types for use with size:

* String (length of string)
* Binary (number of bytes)
* Set (number of elements)
* List (number of child elements)
* Map (number of child elements)

For more information, see the DynamoDB [size](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.OperatorsAndFunctions.html#Expressions.OperatorsAndFunctions.Functions) function\.

## Syntax<a name="ql-functions.size.syntax"></a>

```
size(path)
```

## Arguments<a name="ql-functions.size.arguments"></a>

*path*  
\(Required\) The attribute name or document path\.   
For supported types, see DynamoDB [size](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.OperatorsAndFunctions.html#Expressions.OperatorsAndFunctions.Functions) function\.

## Return type<a name="ql-functions.size.return-type"></a>

`int`

## Examples<a name="ql-functions.size.examples"></a>

```
 SELECT * FROM Orders WHERE OrderID=1 AND size("Image") > 300
```
