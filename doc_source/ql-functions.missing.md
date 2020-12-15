# Using the MISSING Function with PartiQL for DynamoDB<a name="ql-functions.missing"></a>

Returns `TRUE` if the item does not contain the attribute specified\. Only equality and inequality operators can be used with this function\.

## Syntax<a name="ql-functions.missing.syntax"></a>

```
 attributename IS | IS NOT  MISSING 
```

## Arguments<a name="ql-functions.missing.arguments"></a>

*attributename*  
\(Required\) The attribute name to look for\.

## Return type<a name="ql-functions.missing.return-type"></a>

`bool`

## Examples<a name="ql-functions.missing.examples"></a>

```
SELECT * FROM Music WHERE "Awards" is MISSING
```