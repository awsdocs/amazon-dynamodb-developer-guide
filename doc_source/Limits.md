# Limits in DynamoDB<a name="Limits"></a>

This section describes current limits within Amazon DynamoDB \(or no limit, in some cases\)\. Each limit listed below applies on a per\-region basis unless otherwise specified\.

**Topics**
+ [Capacity Units and Provisioned Throughput](#default-limits-capacity-units-provisioned-throughput)
+ [Tables](#limits-tables)
+ [Secondary Indexes](#limits-secondary-indexes)
+ [Partition Keys and Sort Keys](#limits-partition-sort-keys)
+ [Naming Rules](#limits-naming-rules)
+ [Data Types](#limits-data-types)
+ [Items](#limits-items)
+ [Attributes](#limits-attributes)
+ [Expression Parameters](#limits-expression-parameters)
+ [DynamoDB Streams](#limits-dynamodb-streams)
+ [DynamoDB Accelerator \(DAX\)](#limits-dax)
+ [API\-Specific Limits](#limits-api)

## Capacity Units and Provisioned Throughput<a name="default-limits-capacity-units-provisioned-throughput"></a>

### Capacity Unit Sizes<a name="limits-capacity-units"></a>

One read capacity unit = one strongly consistent read per second, or two eventually consistent reads per second, for items up to 4 KB in size\.

One write capacity unit = one write per second, for items up to 1 KB in size\.

### Provisioned Throughput Default Limits<a name="default-limits-throughput"></a>

For any table or global secondary index, the minimum settings for provisioned throughput are 1 read capacity unit and 1 write capacity unit\.

AWS places some default limits on the throughput you can provision\. These are the limits unless you request a higher amount\. To request a service limit increase see [https://aws\.amazon\.com/support](https://aws.amazon.com/support)\.
+ US East \(N\. Virginia\) Region:
  + Per table – 40,000 read capacity units and 40,000 write capacity units
  + Per account – 80,000 read capacity units and 80,000 write capacity units
+ All Other Regions:
  + Per table – 10,000 read capacity units and 10,000 write capacity units
  + Per account – 20,000 read capacity units and 20,000 write capacity units
**Note**  
 All the account's available throughput can be applied to a single table or across multiple tables\. 

The provisioned throughput limit includes the sum of the capacity of the table together with the capacity of all of its global secondary indexes\.

In the AWS Management Console, you can see what your current provisioned capacity is in a given region and make sure you are not too close to the limits\. If you increased your default limits, you can use the [DescribeLimits](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DescribeLimits.html) operation to see the current limit values\.

### Increasing Provisioned Throughput<a name="limits-increasing-provisioned-throughput"></a>

You can increase `ReadCapacityUnits` or `WriteCapacityUnits` as often as necessary, using the AWS Management Console or the `UpdateTable` operation\. In a single call, you can increase the provisioned throughput for a table, for any global secondary indexes on that table, or for any combination of these\. The new settings do not take effect until the `UpdateTable` operation is complete\.

You cannot exceed your per\-account limits when you add provisioned capacity, and DynamoDB will not permit you to increase provisioned capacity extremely rapidly\. Aside from these restrictions, you can increase the provisioned capacity for your tables as high as you need\. For more information about per\-account limits, see the preceding section [Provisioned Throughput Default Limits](#default-limits-throughput)\.

### Decreasing Provisioned Throughput<a name="limits-decreasing-provisioned-throughput"></a>

For every table and global secondary index in an UpdateTable operation, you can decrease `ReadCapacityUnits` or `WriteCapacityUnits` \(or both\)\. The new settings do not take effect until the `UpdateTable` operation is complete\. A decrease is allowed up to four times any time per day\. A day is defined according to the GMT time zone\. Additionally, if there was no decrease in the past hour, an additional decrease is allowed, effectively bringing the maximum number of decreases in a day to 27 times \(4 decreases in the first hour, and 1 decrease for each of the subsequent 1\-hour windows in a day\)\. 

**Important**  
Table and GSI decrease limits are decoupled, so any GSI\(s\) for a particular table have their own decrease limits\. However, if a single request decreases the throughput for a table and a GSI, it will be rejected if either exceeds the current limits\. A request will not be partially processed\. 

**Example**  
 A table with a GSI, in the first 4 hours of a day, can be modified as follows:   
+  Decrease the table's WriteCapacityUnits or ReadCapacityUnits \(or both\) 4 times\. 
+  Decrease the GSI's WriteCapacityUnits or ReadCapacityUnits \(or both\) 4 times\. 
 At the end of that same day the table and the GSI's throughput can potentially be decreased a total of 9 times each\. 

## Tables<a name="limits-tables"></a>

### Table Size<a name="limits-table-size"></a>

There is no practical limit on a table's size\. Tables are unconstrained in terms of the number of items or the number of bytes\.

### Tables Per Account<a name="limits-tables-per-account"></a>

For any AWS account, there is an initial limit of 256 tables per region\.

To request a service limit increase see [https://aws\.amazon\.com/support](https://aws.amazon.com/support)\.

## Secondary Indexes<a name="limits-secondary-indexes"></a>

### Secondary Indexes Per Table<a name="limits-tables-secondary-indexes"></a>

You can define a maximum of 5 local secondary indexes and 5 global secondary indexes per table\.

### Projected Secondary Index Attributes Per Table<a name="limits-tables-projected-secondary-index-attributes"></a>

You can project a total of up to 20 attributes into all of a table's local and global secondary indexes\. This only applies to user\-specified projected attributes\.

In a `CreateTable` operation, if you specify a `ProjectionType` of `INCLUDE`, the total count of attributes specified in `NonKeyAttributes`, summed across all of the secondary indexes, must not exceed 20\. If you project the same attribute name into two different indexes, this counts as two distinct attributes when determining the total\.

This limit does not apply for secondary indexes with a `ProjectionType` of `KEYS_ONLY` or `ALL`\. 

## Partition Keys and Sort Keys<a name="limits-partition-sort-keys"></a>

### Partition Key Length<a name="limits-partition-key-length"></a>

The minimum length of a partition key value is 1 byte\. The maximum length is 2048 bytes\.

### Partition Key Values<a name="limits-partition-key-values"></a>

There is no practical limit on the number of distinct partition key values, for tables or for secondary indexes\.

### Sort Key Length<a name="limits-sort-key-length"></a>

The minimum length of a sort key value is 1 byte\. The maximum length is 1024 bytes\.

### Sort Key Values<a name="limits-sort-key-values"></a>

In general, there is no practical limit on the number of distinct sort key values per partition key value\.

The exception is for tables with local secondary indexes\. With a local secondary index, there is a limit on item collection sizes: For every distinct partition key value, the total sizes of all table and index items cannot exceed 10 GB\. This might constrain the number of sort keys per partition key value\. For more information, see [Item Collection Size Limit](LSI.md#LSI.ItemCollections.SizeLimit)\.

## Naming Rules<a name="limits-naming-rules"></a>

### Table Names and Secondary Index Names<a name="limits-naming-rules-tables-secondary-indexes"></a>

Names for tables and secondary indexes must be at least 3 characters long, but no greater than 255 characters long\. Allowed characters are:
+ `A-Z`
+ `a-z`
+ `0-9`
+ `_` \(underscore\)
+ `-` \(hyphen\)
+ `.` \(dot\)

### Attribute Names<a name="limits-naming-rules-attributes"></a>

In general, an attribute name must be at least 1 character long, but no greater than 64 KB long\.

The exceptions are listed below\. These attribute names must be no greater than 255 characters long:
+ Secondary index partition key names\.
+ Secondary index sort key names\.
+ The names of any user\-specified projected attributes \(applicable only to local secondary indexes\)\. In a `CreateTable` operation, if you specify a `ProjectionType` of `INCLUDE`, then the names of the attributes in the `NonKeyAttributes` parameter are length\-restricted\. The `KEYS_ONLY` and `ALL` projection types are not affected\. 

These attribute names must be encoded using UTF\-8, and the total size of each name \(after encoding\) cannot exceed 255 bytes\.

## Data Types<a name="limits-data-types"></a>

### String<a name="limits-data-types-strings"></a>

The length of a String is constrained by the maximum item size of 400 KB\.

Strings are Unicode with UTF\-8 binary encoding\. Because UTF\-8 is a variable width encoding, DynamoDB determines the length of a String using its UTF\-8 bytes\. 

### Number<a name="limits-data-types-numbers"></a>

A Number can have up to 38 digits of precision, and can be positive, negative, or zero\.
+ Positive range: `1E-130` to `9.9999999999999999999999999999999999999E+125`
+ Negative range: `-9.9999999999999999999999999999999999999E+125` to `-1E-130`

DynamoDB uses JSON strings to represent Number data in requests and replies\. For more information, see [DynamoDB Low\-Level API](Programming.LowLevelAPI.md)\.

If number precision is important, you should pass numbers to DynamoDB using strings that you convert from a number type\.

### Binary<a name="limits-data-types-binary"></a>

The length of a Binary is constrained by the maximum item size of 400 KB\.

Applications that work with Binary attributes must encode the data in Base64 format before sending it to DynamoDB\. Upon receipt of the data, DynamoDB decodes it into an unsigned byte array and uses that as the length of the attribute\. 

## Items<a name="limits-items"></a>

### Item Size<a name="limits-items-size"></a>

The maximum item size in DynamoDB is 400 KB, which includes both attribute name binary length \(UTF\-8 length\) and attribute value lengths \(again binary length\)\. The attribute name counts towards the size limit\.

For example, consider an item with two attributes: one attribute named "shirt\-color" with value "R" and another attribute named "shirt\-size" with value "M"\. The total size of that item is 23 bytes\.

### Item Size for Tables With Local Secondary Indexes<a name="limits-items-size-secondary-indexes"></a>

For each local secondary index on a table, there is a 400 KB limit on the total of the following:
+ The size of an item's data in the table\.
+ The size of the local secondary index entry corresponding to that item, including its key values and projected attributes\.

## Attributes<a name="limits-attributes"></a>

### Attribute Name\-Value Pairs Per Item<a name="limits-attributes-name-value-pairs"></a>

The cumulative size of attributes per item must fit within the maximum DynamoDB item size \(400 KB\)\.

### Number of Values in List, Map, or Set<a name="limits-attributes-number"></a>

There is no limit on the number of values in a List, a Map, or a Set, as long as the item containing the values fits within the 400 KB item size limit\.

### Attribute Values<a name="limits-attributes-values"></a>

An attribute value cannot be an empty String or empty Set \(String Set, Number Set, or Binary Set\)\. However, empty Lists and Maps are allowed\.

### Nested Attribute Depth<a name="limits-attributes-nested-depth"></a>

DynamoDB supports nested attributes up to 32 levels deep\.

## Expression Parameters<a name="limits-expression-parameters"></a>

Expression parameters include `ProjectionExpression`, `ConditionExpression`, `UpdateExpression`, and `FilterExpression`\.

### Lengths<a name="limits-expression-parameters-lengths"></a>

The maximum length of any expression string is 4 KB\. For example, the size of the `ConditionExpression` `a=b` is three bytes\.

The maximum length of any single expression attribute name or expression attribute value is 255 bytes\. For example, `#name` is five bytes; `:val` is four bytes\.

The maximum length of all substitution variables in an expression is 2 MB\. This is the sum of the lengths of all `ExpressionAttributeNames` and `ExpressionAttributeValues`\.

### Operators and Operands<a name="limits-expression-parameters-operators"></a>

The maximum number of operators or functions allowed in an `UpdateExpression` is 300\. For example, the *UpdateExpression* `SET a = :val1 + :val2 + :val3` contains two "`+`" operators\.

The maximum number of operands for the `IN` comparator is 100

### Reserved Words<a name="limits-expression-parameters-reserved-words"></a>

DynamoDB does not prevent you from using names that conflict with reserved words\. \(For a complete list, see [Reserved Words in DynamoDB](ReservedWords.md)\.\)

However, if you use a reserved word in an expression parameter, you must also specify `ExpressionAttributeNames`\. For more information, see [Expression Attribute Names](Expressions.ExpressionAttributeNames.md)\.

## DynamoDB Streams<a name="limits-dynamodb-streams"></a>

### Simultaneous Readers of a Shard in DynamoDB Streams<a name="limits-dynamodb-streams-simultaneous-shard-readers"></a>

Do not allow more than two processes to read from the same DynamoDB Streams shard at the same time\. Exceeding this limit can result in request throttling\.

### Maximum Write Capacity for a Table With a Stream Enabled<a name="limits-dynamodb-streams-max-write-capacity"></a>

AWS places some default limits on the write capacity for DynamoDB tables with Streams enabled\. These are the limits unless you request a higher amount\. To request a service limit increase see [https://aws\.amazon\.com/support](https://aws.amazon.com/support)\.
+ US East \(N\. Virginia\) Region:
  + Per table – 40,000 write capacity units
+ All Other Regions:
  + Per table – 10,000 write capacity units

**Note**  
 The provisioned throughput limits also apply for DynamoDB tables with Streams enabled\. For more information, see [Provisioned Throughput Default Limits](#default-limits-throughput)\. 

## DynamoDB Accelerator \(DAX\)<a name="limits-dax"></a>

### AWS Region Availability<a name="limits-dax-regions"></a>

For a list of regions in which DAX is available, see [DynamoDB Accelerator \(DAX\)](http://docs.aws.amazon.com/general/latest/gr/rande.html#ddb_dax_region) in the *AWS General Reference*\.

### Nodes<a name="limits-dax-nodes"></a>

A DAX cluster consists of exactly 1 primary node, and between 0 and 9 read replica nodes\.

The total number of nodes \(per AWS account\) cannot exceed 50 in a single AWS region\.

### Parameter Groups<a name="limits-dax-parameter-groups"></a>

You can create up to 20 DAX parameter groups per region\.

### Subnet Groups<a name="limits-dax-subnet-groups"></a>

You can create up to 50 DAX subnet groups per region\.

Within a subnet group, you can define up to 20 subnets\.

## API\-Specific Limits<a name="limits-api"></a>

**`CreateTable`/`UpdateTable`/`DeleteTable`**  
In general, you can have up to 10 `CreateTable`, `UpdateTable`, and `DeleteTable` requests running simultaneously \(in any combination\)\. In other words, the total number of tables in the `CREATING`, `UPDATING` or `DELETING` state cannot exceed 10\.  
The only exception is when you are creating a table with one or more secondary indexes\. You can have up to 5 such requests running at a time; however, if the table or index specifications are complex, DynamoDB might temporarily reduce the number of concurrent requests below 5\.

**`BatchGetItem`**  
A single `BatchGetItem` operation can retrieve a maximum of 100 items\. The total size of all the items retrieved cannot exceed 16 MB\.

**`BatchWriteItem`**  
A single `BatchWriteItem` operation can contain up to 25 `PutItem` or `DeleteItem` requests\. The total size of all the items written cannot exceed 16 MB\.

**`DescribeLimits`**  
`DescribeLimits` should only be called periodically\. You can expect throttling errors if you call it more than once in a minute\.

**`Query`**  
The result set from a `Query` is limited to 1 MB per call\. You can use the `LastEvaluatedKey` from the query response to retrieve more results\.

**`Scan`**  
The result set from a `Scan` is limited to 1 MB per call\. You can use the `LastEvaluatedKey` from the scan response to retrieve more results\.