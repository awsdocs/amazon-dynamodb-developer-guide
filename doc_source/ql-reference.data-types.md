# PartiQL data types for DynamoDB<a name="ql-reference.data-types"></a>

The following table lists the data types you can use with PartiQL for DynamoDB\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ql-reference.data-types.html)

## Examples<a name="ql-reference.data-types"></a>

The following statement demonstrates how to insert the following data types: `String`, `Number`, `Map`, `List`, `Number Set` and `String Set`\.

```
INSERT INTO TypesTable value {'primarykey':'1', 
'NumberType':1,
'MapType' : {'entryname1': 'value', 'entryname2': 4}, 
'ListType': [1,'stringval'], 
'NumberSetType':<<1,34,32,4.5>>, 
'StringSetType':<<'stringval','stringval2'>>
}
```

The following statement demonstrates how to insert new elements into the `Map`, `List`, `Number Set` and `String Set` types and change the value of a `Number` type\.

```
UPDATE TypesTable 
SET NumberType=NumberType + 100 
SET MapType.NewMapEntry=[2020, 'stringvalue', 2.4]
SET ListType = LIST_APPEND(ListType, [4, <<'string1', 'string2'>>])
SET NumberSetType= SET_ADD(NumberSetType, <<345, 48.4>>)
SET StringSetType = SET_ADD(StringSetType, <<'stringsetvalue1', 'stringsetvalue2'>>)
WHERE primarykey='1'
```

The following statement demonstrates how to remove elements from the `Map`, `List`, `Number Set` and `String Set` types and change the value of a `Number` type\.

```
UPDATE TypesTable 
SET NumberType=NumberType - 1
REMOVE ListType[1]
REMOVE MapType.NewMapEntry
SET NumberSetType = SET_DELETE( NumberSetType, <<345>>)
SET StringSetType = SET_DELETE( StringSetType, <<'stringsetvalue1'>>)
WHERE primarykey='1'
```

For more information, see [DynamoDB data types](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.NamingRulesDataTypes.html#HowItWorks.DataTypes)\.