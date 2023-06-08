# Higher\-level programming interfaces for DynamoDB<a name="HigherLevelInterfaces"></a>

The AWS SDKs provide applications with low\-level interfaces for working with Amazon DynamoDB\. These client\-side classes and methods correspond directly to the low\-level DynamoDB API\. However, many developers experience a sense of disconnect, or *impedance mismatch*, when they need to map complex data types to items in a database table\. With a low\-level database interface, developers must write methods for reading or writing object data to database tables, and vice versa\. The amount of extra code required for each combination of object type and database table can seem overwhelming\.

To simplify development, the AWS SDKs for Java and \.NET provide additional interfaces with higher levels of abstraction\. The higher\-level interfaces for DynamoDB let you define the relationships between objects in your program and the database tables that store those objects' data\. After you define this mapping, you call simple object methods such as `save`, `load`, or `delete`, and the underlying low\-level DynamoDB operations are automatically invoked on your behalf\. This allows you to write object\-centric code, rather than database\-centric code\.

The higher\-level programming interfaces for DynamoDB are available in the AWS SDKs for Java and \.NET\.

**Java**
+ [Java: DynamoDBMapper](DynamoDBMapper.md)

**\.NET**
+ [\.NET: Document model](DotNetSDKMidLevel.md)
+ [\.NET: Object persistence model](DotNetSDKHighLevel.md)