# Using the EXISTS Function with PartiQL for DynamoDB<a name="ql-functions.exists"></a>

You can use EXISTS to perform the same function as `ConditionCheck` does in the [TransactWriteItems](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/transaction-apis.html#transaction-apis-txwriteitems) API\. The EXISTS function can only be used in transactions\.

Given a value, returns `TRUE` if the value is a non\-empty collection\. Otherwise, returns `FALSE`\.

**Note**  
This function can only be used in transactional operations\.

## Syntax<a name="ql-functions.exists.syntax"></a>

```
EXISTS ( statement )
```

## Arguments<a name="ql-functions.exists.arguments"></a>

*statement*  
\(Required\) The SELECT statement that the function evaluates\.  
The SELECT statement must specify a full primary key and one other condition\.

## Return type<a name="ql-functions.exists.return-type"></a>

`bool`

## Examples<a name="ql-functions.exists.examples"></a>

```
EXISTS(
    SELECT * FROM "Music" 
    WHERE "Artist" = 'Acme Band' AND "SongTitle" = 'PartiQL Rocks')
```