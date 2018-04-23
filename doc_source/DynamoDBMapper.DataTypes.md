# Supported Data Types<a name="DynamoDBMapper.DataTypes"></a>

This section describes the supported primitive Java data types, collections, and arbitrary data types\. 

DynamoDB supports the following primitive data types and primitive wrapper classes\. 
+ `String`
+ `Boolean`, `boolean`
+ `Byte`, `byte`
+ `Date` \(as [ISO\_8601](http://en.wikipedia.org/wiki/ISO_8601) millisecond\-precision string, shifted to UTC\)
+ `Calendar` \(as [ISO\_8601](http://en.wikipedia.org/wiki/ISO_8601) millisecond\-precision string, shifted to UTC\)
+ `Long`, `long`
+ `Integer`, `int`
+ `Double`, `double`
+ `Float`, `float`
+ `BigDecimal`
+ `BigInteger`

**Note**  
 For more information on the DynamoDB naming rules and the various supported data types see [Naming Rules and Data Types](HowItWorks.NamingRulesDataTypes.md)\. 

DynamoDB supports the Java [Set](http://docs.oracle.com/javase/6/docs/api/java/util/Set.html) collection types\. If your mapped collection property is not a Set, then an exception is thrown\.

The following table summarizes how the preceding Java types map to the DynamoDB types\.


****  

| Java type | DynamoDB type | 
| --- | --- | 
|  All number types  |  `N` \(number type\)  | 
|  Strings  |  `S` \(string type\)   | 
| Boolean | BOOL \(boolean type\), 0 or 1\. | 
| ByteBuffer | B \(binary type\) | 
| Date | S \(string type\)\. The Date values are stored as ISO\-8601 formatted strings\. | 
| [Set](http://docs.oracle.com/javase/6/docs/api/java/util/Set.html) collection types | SS \(string set\) type, NS \(number set\) type, or BS \(binary set\) type\. | 

 The `DynamoDBTypeConverter` interface lets you map your own arbitrary data types to a data type that is natively supported by DynamoDB\. For more information, see [Mapping Arbitrary Data](DynamoDBMapper.ArbitraryDataMapping.md)\. 