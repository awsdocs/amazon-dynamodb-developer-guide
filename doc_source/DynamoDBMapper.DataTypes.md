# Supported data types for DynamoDB Mapper for Java<a name="DynamoDBMapper.DataTypes"></a>

This section describes the supported primitive Java data types, collections, and arbitrary data types in Amazon DynamoDB\. 

Amazon DynamoDB supports the following primitive Java data types and primitive wrapper classes\. 
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
For more information about DynamoDB naming rules and the various supported data types, see [Supported data types and naming rules in Amazon DynamoDB](HowItWorks.NamingRulesDataTypes.md)\. 
Empty Binary values are supported by the DynamoDBMapper\.
Empty String values are supported by AWS SDK for Java 2\.x\.  
In AWS SDK for Java 1\.x, DynamoDBMapper supports reading of empty String attribute values, however, it will not write empty String attribute values because these attributes are dropped from the request\.

DynamoDB supports the Java [Set](http://docs.oracle.com/javase/6/docs/api/java/util/Set.html), [List](http://docs.oracle.com/javase/6/docs/api/java/util/List.html), and [Map](http://docs.oracle.com/javase/6/docs/api/java/util/Map.html) collection types\. The following table summarizes how these Java types map to the DynamoDB types\.


****  

| Java type | DynamoDB type | 
| --- | --- | 
|  All number types  |  `N` \(number type\)  | 
|  Strings  |  `S` \(string type\)   | 
| Boolean | BOOL \(Boolean type\), 0 or 1\. | 
| ByteBuffer | B \(binary type\) | 
| Date | S \(string type\)\. The Date values are stored as ISO\-8601 formatted strings\. | 
| [Set](http://docs.oracle.com/javase/6/docs/api/java/util/Set.html) collection types | SS \(string set\) type, NS \(number set\) type, or BS \(binary set\) type\. | 

 The `DynamoDBTypeConverter` interface lets you map your own arbitrary data types to a data type that is natively supported by DynamoDB\. For more information, see [Mapping Arbitrary Data](DynamoDBMapper.ArbitraryDataMapping.md)\. 