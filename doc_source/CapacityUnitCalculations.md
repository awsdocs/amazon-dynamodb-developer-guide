# Item Sizes and Capacity Unit Consumption<a name="CapacityUnitCalculations"></a>

**Topics**
+ [Capacity Unit Consumption for Reads](#ItemSizeCalculations.Reads)
+ [Capacity Unit Consumption for Writes](#ItemSizeCalculations.Writes)

Before you choose read and write capacity settings for your table, you should first understand your data and how your application will access it\. These inputs can help you determine your table's overall storage and throughput needs, and how much throughput capacity your application will require\. DynamoDB tables are schemaless, except for the primary key, so the items in a table can all have different attributes, sizes, and data types\.

The total size of an item is the sum of the lengths of its attribute names and values\. You can use the following guidelines to estimate attribute sizes:
+ Strings are Unicode with UTF\-8 binary encoding\. The size of a string is *\(length of attribute name\) \+ \(number of UTF\-8\-encoded bytes\)*\.
+ Numbers are variable length, with up to 38 significant digits\. Leading and trailing zeroes are trimmed\. The size of a number is approximately *\(length of attribute name\) \+ \(1 byte per two significant digits\) \+ \(1 byte\)*\.
+ A binary value must be encoded in base64 format before it can be sent to DynamoDB, but the value's raw byte length is used for calculating size\. The size of a binary attribute is *\(length of attribute name\) \+ \(number of raw bytes\)\.*
+ The size of a null attribute or a Boolean attribute is *\(length of attribute name\) \+ \(1 byte\)*\.
+ An attribute of type List or Map requires 3 bytes of overhead, regardless of its contents\. The size of a List or Map is *\(length of attribute name\) \+ sum \(size of nested elements\) \+ \(3 bytes\) *\. The size of an empty List or Map is *\(length of attribute name\) \+ \(3 bytes\)*\.

**Note**  
We recommend that you choose shorter attribute names rather than long ones\. This will help you optimize capacity unit consumption and reduce the amount of storage required for your data\.

## Capacity Unit Consumption for Reads<a name="ItemSizeCalculations.Reads"></a>

The following describes how DynamoDB read operations consume read capacity units:
+ `GetItem`—reads a single item from a table\. To determine the number of capacity units GetItem will consume, take the item size and round it up to the next 4 KB boundary\. If you specified a strongly consistent read, this is the number of capacity units required\. For an eventually consistent read \(the default\), take this number and divide it by two\.

  For example, if you read an item that is 3\.5 KB, DynamoDB rounds the item size to 4 KB\. If you read an item of 10 KB, DynamoDB rounds the item size to 12 KB\.
+ `BatchGetItem`—reads up to 100 items, from one or more tables\. DynamoDB processes each item in the batch as an individual `GetItem` request, so DynamoDB first rounds up the size of each item to the next 4 KB boundary, and then calculates the total size\. The result is not necessarily the same as the total size of all the items\. For example, if `BatchGetItem` reads a 1\.5 KB item and a 6\.5 KB item, DynamoDB will calculate the size as 12 KB \(4 KB \+ 8 KB\), not 8 KB \(1\.5 KB \+ 6\.5 KB\)\. 
+ `Query`—reads multiple items that have the same partition key value\. All of the items returned are treated as a single read operation, where DynamoDB computes the total size of all items and then rounds up to the next 4 KB boundary\. For example, suppose your query returns 10 items whose combined size is 40\.8 KB\. DynamoDB rounds the item size for the operation to 44 KB\. If a query returns 1500 items of 64 bytes each, the cumulative size is 96 KB\.
+ `Scan`—reads all of the items in a table\. DynamoDB considers the size of the items that are evaluated, not the size of the items returned by the scan\.

If you perform a read operation on an item that does not exist, DynamoDB will still consume provisioned read throughput: A strongly consistent read request consumes one read capacity unit, while an eventually consistent read request consumes 0\.5 of a read capacity unit\.

For any operation that returns items, you can request a subset of attributes to retrieve; however, doing so has no impact on the item size calculations\. In addition, `Query` and `Scan` can return item counts instead of attribute values\. Getting the count of items uses the same quantity of read capacity units and is subject to the same item size calculations, because DynamoDB has to read each item in order to increment the count\.

### Read Operations and Read Consistency<a name="ItemSizeCalculations.Reads.Consistency"></a>

The preceding calculations assume strongly consistent read requests\. For an eventually consistent read request, the operation consumes only half the capacity units\. For an eventually consistent read, if total item size is 80 KB, the operation consumes only 10 capacity units\.

## Capacity Unit Consumption for Writes<a name="ItemSizeCalculations.Writes"></a>

The following describes how DynamoDB write operations consume write capacity units:
+ `PutItem`—writes a single item to a table\. If an item with the same primary key exists in the table, the operation replaces the item\. For calculating provisioned throughput consumption, the item size that matters is the larger of the two\.
+ `UpdateItem`—modifies a single item in the table\. DynamoDB considers the size of the item as it appears before and after the update\. The provisioned throughput consumed reflects the larger of these item sizes\. Even if you update just a subset of the item's attributes, `UpdateItem` will still consume the full amount of provisioned throughput \(the larger of the "before" and "after" item sizes\)\. 
+ `DeleteItem`—removes a single item from a table\. The provisioned throughput consumption is based on the size of the deleted item\.
+ `BatchWriteItem`—writes up to 25 items to one or more tables\. DynamoDB processes each item in the batch as an individual `PutItem` or `DeleteItem` request \(updates are not supported\), so DynamoDB first rounds up the size of each item to the next 1 KB boundary, and then calculates the total size\. The result is not necessarily the same as the total size of all the items\. For example, if `BatchWriteItem` writes a 500 byte item and a 3\.5 KB item, DynamoDB will calculate the size as 5 KB \(1 KB \+ 4 KB\), not 4 KB \(500 bytes \+ 3\.5 KB\)\.

For `PutItem`, `UpdateItem`, and `DeleteItem` operations, DynamoDB rounds the item size up to the next 1 KB\. For example, if you put or delete an item of 1\.6 KB, DynamoDB rounds the item size up to 2 KB\.

`PutItem`, `UpdateItem`, and `DeleteItem` allow *conditional writes*, where you specify an expression that must evaluate to true in order for the operation to succeed\. If the expression evaluates to false, DynamoDB will still consume write capacity units from the table: 
+ For an existing item, the number of write capacity units consumed depends on the size of the new item\. \(For example, a failed conditional write of a 1 KB item would consume one write capacity unit; if the new item were twice that size, the failed conditional write would consume two write capacity units\.\) 
+ For a new item, DynamoDB will consume one write capacity unit\.