# Using the ATTRIBUTE\_TYPE function with PartiQL for DynamoDB<a name="ql-functions.attribute_type"></a>

Returns `TRUE` if the attribute at the specified path is of a particular data type\.

## Syntax<a name="ql-functions.attribute_type.syntax"></a>

```
attribute_type( attributename, type )
```

## Arguments<a name="ql-functions.attribute_type.arguments"></a>

*attributename*  
\(Required\) The attribute name to use\.

*type*  
\(Required\) The attribute type to check for\. For a list of valid values, see DynamoDB [attribute\_type](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.OperatorsAndFunctions.html#Expressions.OperatorsAndFunctions.Functions)\.

## Return type<a name="ql-functions.attribute_type.return-type"></a>

`bool`

## Examples<a name="ql-functions.attribute_type.examples"></a>

```
SELECT * FROM "Music" WHERE attribute_type("Artist", 'S')
```