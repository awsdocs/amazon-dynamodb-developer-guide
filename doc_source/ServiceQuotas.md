# Service, account, and table quotas in Amazon DynamoDB<a name="ServiceQuotas"></a>

This section describes current quotas, formerly referred to as limits, within Amazon DynamoDB\. Each quota applies on a per\-Region basis unless otherwise specified\.

**Topics**
+ [Read/write capacity mode and throughput](#default-limits-throughput-capacity-modes)
+ [Import quotas](#import-limits)
+ [Tables](#limits-tables)
+ [Global tables](#gt-limits-throughput)
+ [Secondary indexes](#limits-secondary-indexes)
+ [Partition keys and sort keys](#limits-partition-sort-keys)
+ [Naming rules](#limits-naming-rules)
+ [Data types](#limits-data-types)
+ [Items](#limits-items)
+ [Attributes](#limits-attributes)
+ [Expression parameters](#limits-expression-parameters)
+ [DynamoDB transactions](#limits-dynamodb-transactions)
+ [DynamoDB Streams](#limits-dynamodb-streams)
+ [DynamoDB Accelerator \(DAX\)](#limits-dax)
+ [API\-specific limits](#limits-api)
+ [DynamoDB encryption at rest](#limits-dynamodb-encryption)
+ [Table export to Amazon S3](#limits-table-export)
+ [Backup and restore](#limits-backup-restore)

## Read/write capacity mode and throughput<a name="default-limits-throughput-capacity-modes"></a>

You can change this setting twice in a 24\-hour period, so don’t worry if this needs to be changed in the future\.

### Capacity unit sizes \(for provisioned tables\)<a name="limits-capacity-units"></a>

One read capacity unit = one strongly consistent read per second, or two eventually consistent reads per second, for items up to 4 KB in size\.

One write capacity unit = one write per second, for items up to 1 KB in size\.

Transactional read requests require two read capacity units to perform one read per second for items up to 4 KB\.

Transactional write requests require two write capacity units to perform one write per second for items up to 1 KB\.

### Request unit sizes \(for on\-demand tables\)<a name="limits-request-units"></a>

One read request unit = one strongly consistent read, or two eventually consistent reads, for items up to 4 KB in size\.

One write request unit = one write, for items up to 1 KB in size\.

Transactional read requests require two read request units to perform one read for items up to 4 KB\.

Transactional write requests require two write request units to perform one write for items up to 1 KB\.

### Throughput default quotas<a name="default-limits-throughput"></a>

AWS places some default quotas on the throughput your account can provision and consume in a Region\. 

The account\-level read throughput and account\-level write throughput quotas apply at the account level\. These account\-level quotas apply to the sum of the provisioned throughput capacity for all your account’s tables and global secondary indexes in a given Region\. All the account's available throughput can be provisioned for a single table or across multiple tables\. These quotas only apply to tables using the provisioned capacity mode\.

The table\-level read throughput and table\-level write throughput quotas apply at a table level\. These table\-level quotas apply to tables that use the provisioned capacity mode as well as tables that use the on\-demand capacity mode\. These table\-level quotas apply to the sum of a table’s throughput together with the throughput of all of its global secondary indexes\.

Below are the throughput quotas that apply on your account, by default\.


|  | On\-Demand | Provisioned | Adjustable | 
| --- | --- | --- | --- | 
| `Per table` | `40,000 read request units and 40,000 write request units` | `40,000 read capacity units and 40,000 write capacity units` | Yes | 
| `Per account` | `Not applicable` | `80,000 read capacity units and 80,000 write capacity units` | Yes | 
| `Minimum throughput for any table or global secondary index` | `Not applicable` | `1 read capacity unit and 1 write capacity unit` | Yes | 

You can use the [Service Quotas console](https://console.aws.amazon.com/servicequotas), the [AWS API](https://docs.aws.amazon.com/servicequotas/2019-06-24/apireference/Welcome.html) and the [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide) to request quota increases for the adjustable quotas when needed\. 

For your account\-level throughput quotas, you can use the [Service Quotas console](https://console.aws.amazon.com/servicequotas), the [AWS CloudWatch console](https://console.aws.amazon.com/cloudwatch), the [AWS API](https://docs.aws.amazon.com/servicequotas/2019-06-24/apireference/Welcome.html) and the [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide) to create CloudWatch alarms and be notified automatically when your current usage reaches a specified percentage of your applied quota values\. Using CloudWatch you can also monitor your usage by looking at the `AccountProvisionedReadCapacityUnits` and `AccountProvisionedWriteCapacityUnits` AWS usage metrics\. To learn more about usage metrics, see [AWS usage metrics\.](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Service-Quota-Integration.html)

### Increasing or decreasing throughput \(for provisioned tables\)<a name="decreasing-increasing-throughput"></a>

#### Increasing provisioned throughput<a name="limits-increasing-provisioned-throughput"></a>

You can increase `ReadCapacityUnits` or `WriteCapacityUnits` as often as necessary, using the AWS Management Console or the `UpdateTable` operation\. In a single call, you can increase the provisioned throughput for a table, for any global secondary indexes on that table, or for any combination of these\. The new settings do not take effect until the `UpdateTable` operation is complete\.

You can't exceed your per\-account quotas when you add provisioned capacity, and DynamoDB doesn't allow you to increase provisioned capacity very rapidly\. Aside from these restrictions, you can increase the provisioned capacity for your tables as high as you need\. For more information about per\-account quotas, see the preceding section, [Throughput default quotas](#default-limits-throughput)\.

#### Decreasing provisioned throughput<a name="limits-decreasing-provisioned-throughput"></a>

For every table and global secondary index in an `UpdateTable` operation, you can decrease `ReadCapacityUnits` or `WriteCapacityUnits` \(or both\)\. The new settings don't take effect until the `UpdateTable` operation is complete\. 

There is a default quota on the number of provisioned capacity decreases you can perform on your DynamoDB table per day\. A day is defined according to Universal Time Coordinated \(UTC\)\. On a given day, you can start by performing up to four decreases within one hour as long as you have not performed any other decreases yet during that day\. Subsequently, you can perform one additional decrease per hour as long as there was no decreases in the preceding hour\. This effectively brings the maximum number of decreases in a day to 27 times \(4 decreases in the first hour, and 1 decrease for each of the subsequent 1\-hour windows in a day\)\.

You can use the [Service Quotas console](https://console.aws.amazon.com/servicequotas), the [AWS API](https://docs.aws.amazon.com/servicequotas/2019-06-24/apireference/Welcome.html) and the [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide) to request quota increases, when needed\.

**Important**  
Table and global secondary index decrease limits are decoupled, so any global secondary indexes for a particular table have their own decrease limits\. However, if a single request decreases the throughput for a table and a global secondary index, it is rejected if either exceeds the current limits\. Requests are not partially processed\. 

**Example**  
In the first 4 hours of a day, a table with a global secondary index can be modified as follows:   
+  Decrease the table's `WriteCapacityUnits` or `ReadCapacityUnits` \(or both\) four times\. 
+  Decrease the `WriteCapacityUnits` or `ReadCapacityUnits` \(or both\) of the global secondary index four times\. 
 At the end of that same day, the table and the global secondary index throughput can potentially be decreased a total of 27 times each\. 

## Import quotas<a name="import-limits"></a>

DynamoDB Import from Amazon S3 can support up to 50 concurrent import jobs with a total import source object size of 15TB at a time in us\-east\-1, us\-west\-2, and eu\-west\-1 regions\. In all other regions, up to 50 concurrent import tasks with a total size of 1TB is supported\. Each import job can take up to 5000 Amazon S3 objects in all regions\. For more information on import and validation, see [ import format quotas and validation](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/S3DataImport.Validation.html#S3DataImport.Validation.limits)\.

## Tables<a name="limits-tables"></a>

### Table size<a name="limits-table-size"></a>

There is no practical limit on a table's size\. Tables are unconstrained in terms of the number of items or the number of bytes\.

### Maximum number of tables per account per region<a name="limits-tables-per-account"></a>

For any AWS account, there is an initial quota of 2,500 tables per AWS Region\. 

You can use the [Service Quotas console](https://console.aws.amazon.com/servicequotas), the [AWS API](https://docs.aws.amazon.com/servicequotas/2019-06-24/apireference/Welcome.html) and the [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide) to view the default and applied quota values for the maximum number of tables for in your account, and to request quota increases, when needed\. You can also request quota increases by cutting a ticket to [AWS support](https://aws.amazon.com/support)

Using the [Service Quotas console](https://console.aws.amazon.com/servicequotas), the [AWS API](https://docs.aws.amazon.com/servicequotas/2019-06-24/apireference/Welcome.html) and the [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide) you can create CloudWatch alarms to get notified automatically when your current usage reaches a specified percentage of your current quota\. Using CloudWatch you can also monitor your usage by looking at the `TableCount` AWS usage metrics\. To learn more about usage metrics, see [AWS usage metrics\.](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Service-Quota-Integration.html) 

## Global tables<a name="gt-limits-throughput"></a>

AWS places some default quotas on the throughput you can provision or utilize when using global tables\.


|  | On\-Demand | Provisioned | 
| --- | --- | --- | 
| `Per table` | `40,000 read request units and 40,000 write request units` | `40,000 read capacity units and 40,000 write capacity units` | 
| `Per table, per destination Region, per day` | `10 TB for all source tables to which a replica was added for this destination Region` | `10 TB for all source tables to which a replica was added for this destination Region` | 

If you are adding a replica for a table that is configured to use more than 40,000 write capacity units \(WCU\), you must request a service quota increase for your add replica WCU quota\. To request a service quota increase see [https://aws\.amazon\.com/support](https://aws.amazon.com/support)\. 

If you are adding a replica or replicas to one destination Region within a 24\-hour period with a combined total greater than 10TB, you must request a service quota increase for your add replica data backfill quota\.

Transactional operations provide atomicity, consistency, isolation, and durability \(ACID\) guarantees only within the AWS Region where the write is made originally\. Transactions are not supported across Regions in global tables\. For example, suppose that you have a global table with replicas in the US East \(Ohio\) and US West \(Oregon\) Regions and you perform a TransactWriteItems operation in the US East \(N\. Virginia\) Region\. In this case, you might observe partially completed transactions in the US West \(Oregon\) Region as changes are replicated\. Changes are replicated to other Regions only after they have been committed in the source Region\. 

## Secondary indexes<a name="limits-secondary-indexes"></a>

### Secondary indexes per table<a name="limits-tables-secondary-indexes"></a>

You can define a maximum of 5 local secondary indexes\. 

 There is a default quota of 20 global secondary indexes per table\. You can use the [Service Quotas console](https://console.aws.amazon.com/servicequotas), the [AWS API](https://docs.aws.amazon.com/servicequotas/2019-06-24/apireference/Welcome.html) and the [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide) to check the global secondary indexes per table default and current quotas that apply for your account, and to request quota increases, when needed\. You can also request quota increases by cutting a ticket to [https://aws\.amazon\.com/support](https://aws.amazon.com/support)\. 

You can create or delete only one global secondary index per `UpdateTable` operation\.

### Projected Secondary Index attributes per table<a name="limits-tables-projected-secondary-index-attributes"></a>

You can project a total of up to 100 attributes into all of a table's local and global secondary indexes\. This only applies to user\-specified projected attributes\.

In a `CreateTable` operation, if you specify a `ProjectionType` of `INCLUDE`, the total count of attributes specified in `NonKeyAttributes`, summed across all of the secondary indexes, must not exceed 100\. If you project the same attribute name into two different indexes, this counts as two distinct attributes when determining the total\.

This limit does not apply for secondary indexes with a `ProjectionType` of `KEYS_ONLY` or `ALL`\. 

## Partition keys and sort keys<a name="limits-partition-sort-keys"></a>

### Partition key length<a name="limits-partition-key-length"></a>

The minimum length of a partition key value is 1 byte\. The maximum length is 2048 bytes\.

### Partition key values<a name="limits-partition-key-values"></a>

There is no practical limit on the number of distinct partition key values, for tables or for secondary indexes\.

### Sort key length<a name="limits-sort-key-length"></a>

The minimum length of a sort key value is 1 byte\. The maximum length is 1024 bytes\.

### Sort key values<a name="limits-sort-key-values"></a>

In general, there is no practical limit on the number of distinct sort key values per partition key value\.

The exception is for tables with secondary indexes\. An item collection is the set of items which have the same value of partition key attribute\. In a global secondary index the item collection is independent of the base table \(and can have a different partition key attribute\), but in a local secondary index the indexed view is colocated in the same partition as the item in the table and shares the same partition key attribute\. As a result of this locality, when a table has one or more LSIs, the item collection cannot be distributed to multiple partitions\.

 For a table with one or more LSIs, no item collection \(including all base table items and all projected LSI views which have the same value of the partition key attribute\) can exceed 10GB in size\. This is because 10GB is the maximum size of a partition\. For more detailed information, see [Item collection size limit](LSI.md#LSI.ItemCollections.SizeLimit)\.

## Naming rules<a name="limits-naming-rules"></a>

### Table names and Secondary Index names<a name="limits-naming-rules-tables-secondary-indexes"></a>

Names for tables and secondary indexes must be at least 3 characters long, but no greater than 255 characters long\. The following are the allowed characters:
+ `A-Z`
+ `a-z`
+ `0-9`
+ `_` \(underscore\)
+ `-` \(hyphen\)
+ `.` \(dot\)

### Attribute names<a name="limits-naming-rules-attributes"></a>

In general, an attribute name must be at least one character long, but no greater than 64 KB long\.

The following are the exceptions\. These attribute names must be no greater than 255 characters long:
+ Secondary index partition key names\.
+ Secondary index sort key names\.
+ The names of any user\-specified projected attributes \(applicable only to local secondary indexes\)\. In a `CreateTable` operation, if you specify a `ProjectionType` of `INCLUDE`, the names of the attributes in the `NonKeyAttributes` parameter are length\-restricted\. The `KEYS_ONLY` and `ALL` projection types are not affected\. 

These attribute names must be encoded using UTF\-8, and the total size of each name \(after encoding\) cannot exceed 255 bytes\.

## Data types<a name="limits-data-types"></a>

### String<a name="limits-data-types-strings"></a>

The length of a String is constrained by the maximum item size of 400 KB\.

Strings are Unicode with UTF\-8 binary encoding\. Because UTF\-8 is a variable width encoding, DynamoDB determines the length of a String using its UTF\-8 bytes\. 

### Number<a name="limits-data-types-numbers"></a>

A Number can have up to 38 digits of precision, and can be positive, negative, or zero\.
+ Positive range: `1E-130` to `9.9999999999999999999999999999999999999E+125`
+ Negative range: `-9.9999999999999999999999999999999999999E+125` to `-1E-130`

DynamoDB uses JSON strings to represent Number data in requests and replies\. For more information, see [DynamoDB low\-level API](Programming.LowLevelAPI.md)\.

If number precision is important, you should pass numbers to DynamoDB using strings that you convert from a number type\.

### Binary<a name="limits-data-types-binary"></a>

The length of a Binary is constrained by the maximum item size of 400 KB\.

Applications that work with Binary attributes must encode the data in base64 format before sending it to DynamoDB\. Upon receipt of the data, DynamoDB decodes it into an unsigned byte array and uses that as the length of the attribute\. 

## Items<a name="limits-items"></a>

### Item size<a name="limits-items-size"></a>

The maximum item size in DynamoDB is 400 KB, which includes both attribute name binary length \(UTF\-8 length\) and attribute value lengths \(again binary length\)\. The attribute name counts towards the size limit\.

For example, consider an item with two attributes: one attribute named "shirt\-color" with value "R" and another attribute named "shirt\-size" with value "M"\. The total size of that item is 23 bytes\.

### Item size for tables with Local Secondary Indexes<a name="limits-items-size-secondary-indexes"></a>

For each local secondary index on a table, there is a 400 KB limit on the total of the following:
+ The size of an item's data in the table\.
+ The size of corresponding entries \(including key values and projected attributes\) in all local secondary indexes\. 

## Attributes<a name="limits-attributes"></a>

### Attribute name\-value pairs per item<a name="limits-attributes-name-value-pairs"></a>

The cumulative size of attributes per item must fit within the maximum DynamoDB item size \(400 KB\)\.

### Number of values in list, map, or set<a name="limits-attributes-number"></a>

There is no limit on the number of values in a List, a Map, or a Set, as long as the item containing the values fits within the 400 KB item size limit\.

### Attribute values<a name="limits-attributes-values"></a>

Empty String and Binary attribute values are allowed, if the attribute is not used as a key attribute for a table or index\. Empty String and Binary values are allowed inside Set, List, and Map types\. An attribute value cannot be an an empty Set \(String Set, Number Set, or Binary Set\)\. However, empty Lists and Maps are allowed\.

### Nested attribute depth<a name="limits-attributes-nested-depth"></a>

DynamoDB supports nested attributes up to 32 levels deep\.

## Expression parameters<a name="limits-expression-parameters"></a>

Expression parameters include `ProjectionExpression`, `ConditionExpression`, `UpdateExpression`, and `FilterExpression`\.

### Lengths<a name="limits-expression-parameters-lengths"></a>

The maximum length of any expression string is 4 KB\. For example, the size of the `ConditionExpression` `a=b` is 3 bytes\.

The maximum length of any single expression attribute name or expression attribute value is 255 bytes\. For example, `#name` is 5 bytes; `:val` is 4 bytes\.

The maximum length of all substitution variables in an expression is 2 MB\. This is the sum of the lengths of all `ExpressionAttributeNames` and `ExpressionAttributeValues`\.

### Operators and operands<a name="limits-expression-parameters-operators"></a>

The maximum number of operators or functions allowed in an `UpdateExpression` is 300\. For example, the *UpdateExpression* `SET a = :val1 + :val2 + :val3` contains two "`+`" operators\.

The maximum number of operands for the `IN` comparator is 100\.

### Reserved words<a name="limits-expression-parameters-reserved-words"></a>

DynamoDB does not prevent you from using names that conflict with reserved words\. \(For a complete list, see [Reserved words in DynamoDB](ReservedWords.md)\.\)

However, if you use a reserved word in an expression parameter, you must also specify `ExpressionAttributeNames`\. For more information, see [Expression attribute names in DynamoDB](Expressions.ExpressionAttributeNames.md)\.

## DynamoDB transactions<a name="limits-dynamodb-transactions"></a>

DynamoDB transactional API operations have the following constraints:
+ A transaction cannot contain more than 100 unique items\.
+ A transaction cannot contain more than 4 MB of data\.
+ No two actions in a transaction can work against the same item in the same table\. For example, you cannot both `ConditionCheck` and `Update` the same item in one transaction\.
+ A transaction cannot operate on tables in more than one AWS account or Region\.
+ Transactional operations provide atomicity, consistency, isolation, and durability \(ACID\) guarantees only within the AWS Region where the write is made originally\. Transactions are not supported across Regions in global tables\. For example, suppose that you have a global table with replicas in the US East \(Ohio\) and US West \(Oregon\) Regions and you perform a `TransactWriteItems` operation in the US East \(N\. Virginia\) Region\. In this case, you might observe partially completed transactions in the US West \(Oregon\) Region as changes are replicated\. Changes are replicated to other Regions only after they have been committed in the source Region\. 

## DynamoDB Streams<a name="limits-dynamodb-streams"></a>

### Simultaneous readers of a shard in DynamoDB Streams<a name="limits-dynamodb-streams-simultaneous-shard-readers"></a>

For single\-Region tables that are not global tables, you can design for up to two processes to read from the same DynamoDB Streams shard at the same time\. Exceeding this limit can result in request throttling\. For global tables, we recommend you limit the number of simultaneous readers to one to avoid request throttling\.

### Maximum write capacity for a table with DynamoDB Streams enabled<a name="limits-dynamodb-streams-max-write-capacity"></a>

AWS places some default quotas on the write capacity for DynamoDB tables with DynamoDB Streams enabled\. These default quotas are applicable only for tables in provisioned read/write capacity mode\. The following are throughput quotas that apply to your account by default\.
+ US East \(N\. Virginia\), US East \(Ohio\), US West \(N\. California\), US West \(Oregon\), South America \(São Paulo\), Europe \(Frankfurt\), Europe \(Ireland\), Asia Pacific \(Tokyo\), Asia Pacific \(Seoul\), Asia Pacific \(Singapore\), Asia Pacific \(Sydney\), China \(Beijing\) Regions:
  + Per table – 40,000 write capacity units
+ All other Regions:
  + Per table – 10,000 write capacity units

You can use the [Service Quotas console](https://console.aws.amazon.com/servicequotas), the [AWS API](https://docs.aws.amazon.com/servicequotas/2019-06-24/apireference/Welcome.html) and the [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide) to check the maximum write capacity for a table with DynamoDB Streams enabled default and current quotas that apply on your account, and to request quota increases, when needed\. You can also request quota increases by cutting a ticket to [AWS support](https://aws.amazon.com/support)\.

**Note**  
 The provisioned throughput quotas also apply for DynamoDB tables with DynamoDB Streams enabled\. When you request a quota increase on the write capacity for a table with Streams enabled, make sure you also request an increase of the provisioned throughput capacity for this table\. For more information, see [ Throughput Default Quotas](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ServiceQuotas.html#default-limits-throughput)\. Other quotas also apply when processing higher throughput DynamoDB Streams\. For more information, see [ Amazon DynamoDB Streams API reference guide](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Operations_Amazon_DynamoDB_Streams.html)\.

## DynamoDB Accelerator \(DAX\)<a name="limits-dax"></a>

### AWS region availability<a name="limits-dax-regions"></a>

For a list of AWS Regions in which DAX is available, see [DynamoDB Accelerator \(DAX\)](https://docs.aws.amazon.com/general/latest/gr/rande.html#ddb_dax_region) in the *AWS General Reference*\.

### Nodes<a name="limits-dax-nodes"></a>

A DAX cluster consists of exactly one primary node, and between zero and ten read replica nodes\.

The total number of nodes \(per AWS account\) cannot exceed 50 in a single AWS Region\.

### Parameter groups<a name="limits-dax-parameter-groups"></a>

You can create up to 20 DAX parameter groups per Region\.

### Subnet groups<a name="limits-dax-subnet-groups"></a>

You can create up to 50 DAX subnet groups per Region\.

Within a subnet group, you can define up to 20 subnets\.

## API\-specific limits<a name="limits-api"></a>

**`CreateTable`/`UpdateTable`/`DeleteTable`**  
In general, you can have up to 500 `CreateTable`, `UpdateTable`, and `DeleteTable` requests running simultaneously in any combination\. As a result, the total number of tables in the `CREATING`, `UPDATING`, or `DELETING` state cannot exceed 500\.  
You can use the [Service Quotas console](https://console.aws.amazon.com/servicequotas), the [AWS API](https://docs.aws.amazon.com/servicequotas/2019-06-24/apireference/Welcome.html) and the [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide) to view default and applied quota values for concurrent control plane operations, and request quota increases, when needed\. You can also request quota increases by cutting a ticket to [AWS support](https://aws.amazon.com/support)

**`BatchGetItem`**  
A single `BatchGetItem` operation can retrieve a maximum of 100 items\. The total size of all the items retrieved cannot exceed 16 MB\.

**`BatchWriteItem`**  
A single `BatchWriteItem` operation can contain up to 25 `PutItem` or `DeleteItem` requests\. The total size of all the items written cannot exceed 16 MB\.

**`DescribeTableReplicaAutoScaling`**  
`DescribeTableReplicaAutoScaling` method supports only 10 requests per second\.

**`DescribeLimits`**  
`DescribeLimits` should be called only periodically\. You can expect throttling errors if you call it more than once in a minute\.

**`DescribeContributorInsights`/`ListContributorInsights`/`UpdateContributorInsights`**  
`DescribeContributorInsights`, `ListContributorInsights` and `UpdateContributorInsights` should be called only periodically\. DynamoDB supports up to five requests per second for each of these APIs\. 

**`Query`**  
The result set from a `Query` is limited to 1 MB per call\. You can use the `LastEvaluatedKey` from the query response to retrieve more results\.

**`Scan`**  
The result set from a `Scan` is limited to 1 MB per call\. You can use the `LastEvaluatedKey` from the scan response to retrieve more results\.

**`UpdateTableReplicaAutoScaling`**  
`UpdateTableReplicaAutoScaling` method supports only ten requests per second\.

**`UpdateTableTimeToLive`**  
The `UpdateTableTimeToLive` method supports only one request to enable or disable `Time to Live (TTL)` per specified table per hour\. This change can take up to one hour to fully process\. Any additional `UpdateTimeToLive` calls for the same table during this one hour duration result in a ValidationException\. 

## DynamoDB encryption at rest<a name="limits-dynamodb-encryption"></a>

You can switch between an AWS owned key, an AWS managed key, and a customer managed key up to four times, anytime per 24\-hour window, on a per table basis, starting from when the table was created\. If there was no change in the past six hours, an additional change is allowed\. This effectively brings the maximum number of changes in a day to eight \(four changes in the first six hours, and one change for each of the subsequent six hour windows in a day\)\.

You can switch encryption keys to use an AWS owned key as often as necessary, even if the above quota has been exhausted\.

These are the quotas unless you request a higher amount\. To request a service quota increase, see [https://aws\.amazon\.com/support](https://aws.amazon.com/support)\.

## Table export to Amazon S3<a name="limits-table-export"></a>

Up to 300 concurrent export tasks, or up to a total of 100 TB from all in\-flight table exports, can be exported\. Both of these limits are checked before an export is queued\.

## Backup and restore<a name="limits-backup-restore"></a>

When restoring through DynamoDB on\-demand backup, you can execute up to 4 concurrent restores that total 50TB\. When restoring through AWS Backup, you can execute up to 4 concurrent restores that total 25TB\. For more information about backups, see [Using On\-Demand backup and restore for DynamoDB](BackupRestore.md)\.