# Using the BEGINS\_WITH function with PartiQL for DynamoDB<a name="ql-functions.beginswith"></a>

Returns `TRUE` if the attribute specified begins with a particular substring\.

## Syntax<a name="ql-functions.beginswith.syntax"></a>

```
begins_with(path, value )
```

## Arguments<a name="ql-functions.beginswith.arguments"></a>

*path*  
\(Required\) The attribute name or document path to use\.

*value*  
\(Required\) The string to search for\.

## Return type<a name="ql-functions.beginswith.return-type"></a>

`bool`

## Examples<a name="ql-functions.beginswith.examples"></a>

```
SELECT * FROM "Orders" WHERE "OrderID"=1 AND begins_with("Address", '7834 24th')
```