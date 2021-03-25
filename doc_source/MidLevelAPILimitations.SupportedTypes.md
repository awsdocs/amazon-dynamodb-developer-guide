# Supported Data Types<a name="MidLevelAPILimitations.SupportedTypes"></a>

The document model supports a set of primitive \.NET data types and collections data types\. The model supports the following primitive data types\. 
+ `bool`
+ `byte` 
+ `char`
+ `DateTime`
+ `decimal`
+ `double`
+ `float`
+ `Guid`
+ `Int16`
+ `Int32`
+ `Int64`
+ `SByte`
+ `string`
+ `UInt16`
+ `UInt32`
+ `UInt64`

The following table summarizes the mapping of the preceding \.NET types to the DynamoDB types\.


****  

| \.NET primitive type | DynamoDB type | 
| --- | --- | 
|  All number types  |  `N` \(number type\)  | 
|  All string types  |  `S` \(string type\)   | 
|  MemoryStream, byte\[\]  |  `B` \(binary type\)   | 
| bool | N \(number type\)\. 0 represents false and 1 represents true\. | 
| DateTime | S \(string type\)\. The DateTime values are stored as ISO\-8601 formatted strings\. | 
| Guid | S \(string type\)\. | 
| Collection types \(List, HashSet, and array\) | BS \(binary set\) type, SS \(string set\) type, and NS \(number set\) type | 

AWS SDK for \.NET defines types for mapping DynamoDB's Boolean, null, list and map types to \.NET document model API:
+ Use `DynamoDBBool` for Boolean type\.
+ Use `DynamoDBNull` for null type\.
+ Use `DynamoDBList` for list type\.
+ Use `Document` for map type\.

**Note**  
Empty binary values are supported\.
Reading of empty string values is supported\. Empty string attribute values are supported within attribute values of string Set type while writing to DynamoDB\. Empty string attribute values of string type and empty string values contained within List or Map type are dropped from write requests