# Supported data types and naming rules in Amazon DynamoDB<a name="HowItWorks.NamingRulesDataTypes"></a>

This section describes the Amazon DynamoDB naming rules and the various data types that DynamoDB supports\. There are limits that apply to data types\. For more information, see [Data types](ServiceQuotas.md#limits-data-types)\. 

**Topics**
+ [Naming rules](#HowItWorks.NamingRules)
+ [Data types](#HowItWorks.DataTypes)

## Naming rules<a name="HowItWorks.NamingRules"></a>

Tables, attributes, and other objects in DynamoDB must have names\. Names should be meaningful and concise—for example, names such as *Products*, *Books*, and *Authors* are self\-explanatory\.

The following are the naming rules for DynamoDB:
+ All names must be encoded using UTF\-8, and are case\-sensitive\.
+ Table names and index names must be between 3 and 255 characters long, and can contain only the following characters: 
  + `a-z`
  + `A-Z `
  + ` 0-9 `
  + `_` \(underscore\)
  + `-` \(dash\)
  + `.` \(dot\)
+ Attribute names must be at least one character long, and less than 64 KB in size\. It is considered best practice to keep your attribute names as short as possible\. This helps to reduce read request units consumed, as attribute names are included in metering of storage and throughput usage\.

  The following are the exceptions\. These attribute names must be no greater than 255 characters long:
  + Secondary index partition key names\.
  + Secondary index sort key names\.
  + The names of any user\-specified projected attributes \(applicable only to local secondary indexes\)\. 

### Reserved words and special characters<a name="HowItWorks.NamingRules.Reserved"></a>

DynamoDB has a list of reserved words and special characters\. For a complete list of reserved words in DynamoDB, see [Reserved words in DynamoDB](ReservedWords.md)\. Also, the following characters have special meaning in DynamoDB: **\#** \(hash\) and **:** \(colon\)\.

Although DynamoDB allows you to use these reserved words and special characters for names, we recommend that you avoid doing so because you have to define placeholder variables whenever you use these names in an expression\. For more information, see [Expression attribute names in DynamoDB](Expressions.ExpressionAttributeNames.md)\.

## Data types<a name="HowItWorks.DataTypes"></a>

DynamoDB supports many different data types for attributes within a table\. They can be categorized as follows:
+ **Scalar Types** – A scalar type can represent exactly one value\. The scalar types are number, string, binary, Boolean, and null\.
+ **Document Types** – A document type can represent a complex structure with nested attributes, such as you would find in a JSON document\. The document types are list and map\.
+ **Set Types** – A set type can represent multiple scalar values\. The set types are string set, number set, and binary set\.

When you create a table or a secondary index, you must specify the names and data types of each primary key attribute \(partition key and sort key\)\. Furthermore, each primary key attribute must be defined as type string, number, or binary\.

DynamoDB is a NoSQL database and is *schemaless*\. This means that, other than the primary key attributes, you don't have to define any attributes or data types when you create tables\. By comparison, relational databases require you to define the names and data types of each column when you create a table\.

The following are descriptions of each data type, along with examples in JSON format\.

### Scalar types<a name="HowItWorks.DataTypes.Scalar"></a>

The scalar types are number, string, binary, Boolean, and null\.

#### Number<a name="HowItWorks.DataTypes.Number"></a>

Numbers can be positive, negative, or zero\. Numbers can have up to 38 digits of precision\. Exceeding this results in an exception\. If you need greater precision than 38 digits, you can use strings\.
+ Positive range: 1E\-130 to 9\.9999999999999999999999999999999999999E\+125
+ Negative range: \-9\.9999999999999999999999999999999999999E\+125 to \-1E\-130

In DynamoDB, numbers are represented as variable length\. Leading and trailing zeroes are trimmed\.

All numbers are sent across the network to DynamoDB as strings, to maximize compatibility across languages and libraries\. However, DynamoDB treats them as number type attributes for mathematical operations\. 

You can use the number data type to represent a date or a timestamp\. One way to do this is by using epoch time—the number of seconds since 00:00:00 UTC on 1 January 1970\. For example, the epoch time `1437136300` represents 12:31:40 PM UTC on 17 July 2015\.

For more information, see [http://en\.wikipedia\.org/wiki/Unix\_time](http://en.wikipedia.org/wiki/Unix_time)\.

#### String<a name="HowItWorks.DataTypes.String"></a>

Strings are Unicode with UTF\-8 binary encoding\. The minimum length of a string can be zero, if the attribute is not used as a key for an index or table, and is constrained by the maximum DynamoDB item size limit of 400 KB\.

The following additional constraints apply to primary key attributes that are defined as type string:
+ For a simple primary key, the maximum length of the first attribute value \(the partition key\) is 2048 bytes\.
+ For a composite primary key, the maximum length of the second attribute value \(the sort key\) is 1024 bytes\.

DynamoDB collates and compares strings using the bytes of the underlying UTF\-8 string encoding\. For example, "`a`" \(0x61\) is greater than "`A`" \(0x41\), and "`¿`" \(0xC2BF\) is greater than "`z`" \(0x7A\)\.

You can use the string data type to represent a date or a timestamp\. One way to do this is by using ISO 8601 strings, as shown in these examples:
+ `2016-02-15`
+ `2015-12-21T17:42:34Z`
+ `20150311T122706Z`

For more information, see [http://en\.wikipedia\.org/wiki/ISO\_8601](http://en.wikipedia.org/wiki/ISO_8601)\.

**Note**  
Unlike conventional relational databases, DynamoDB does not natively support a date and time data type\. It can be useful instead to store data and time data as a number data type, using Unix epoch time\.

#### Binary<a name="HowItWorks.DataTypes.Binary"></a>

Binary type attributes can store any binary data, such as compressed text, encrypted data, or images\. Whenever DynamoDB compares binary values, it treats each byte of the binary data as unsigned\.

The length of a binary attribute can be zero, if the attribute is not used as a key for an index or table, and is constrained by the maximum DynamoDB item size limit of 400 KB\.

If you define a primary key attribute as a binary type attribute, the following additional constraints apply:
+ For a simple primary key, the maximum length of the first attribute value \(the partition key\) is 2048 bytes\.
+ For a composite primary key, the maximum length of the second attribute value \(the sort key\) is 1024 bytes\.

Your applications must encode binary values in base64\-encoded format before sending them to DynamoDB\. Upon receipt of these values, DynamoDB decodes the data into an unsigned byte array and uses that as the length of the binary attribute\. 

The following example is a binary attribute, using base64\-encoded text\.

```
dGhpcyB0ZXh0IGlzIGJhc2U2NC1lbmNvZGVk
```

#### Boolean<a name="HowItWorks.DataTypes.Boolean"></a>

A Boolean type attribute can store either `true` or `false`\.

#### Null<a name="HowItWorks.DataTypes.Null"></a>

Null represents an attribute with an unknown or undefined state\.

### Document types<a name="HowItWorks.DataTypes.Document"></a>

The document types are list and map\. These data types can be nested within each other, to represent complex data structures up to 32 levels deep\. 

There is no limit on the number of values in a list or a map, as long as the item containing the values fits within the DynamoDB item size limit \(400 KB\)\.

 An attribute value can be an empty string or empty binary value if the attribute is not used for a table or index key\. An attribute value cannot be an empty set \(string set, number set, or binary set\), however, empty lists and maps are allowed\. Empty string and binary values are allowed within lists and maps\. For more information, see [Attributes](ServiceQuotas.md#limits-attributes)\. 

#### List<a name="HowItWorks.DataTypes.Document.List"></a>

A list type attribute can store an ordered collection of values\. Lists are enclosed in square brackets: `[ ... ]`

A list is similar to a JSON array\. There are no restrictions on the data types that can be stored in a list element, and the elements in a list element do not have to be of the same type\.

The following example shows a list that contains two strings and a number\.

```
FavoriteThings: ["Cookies", "Coffee", 3.14159]
```

**Note**  
DynamoDB lets you work with individual elements within lists, even if those elements are deeply nested\. For more information, see [Using expressions in DynamoDB](Expressions.md)\.

#### Map<a name="HowItWorks.DataTypes.Document.Map"></a>

A map type attribute can store an unordered collection of name\-value pairs\. Maps are enclosed in curly braces: `{ ... }`

A map is similar to a JSON object\. There are no restrictions on the data types that can be stored in a map element, and the elements in a map do not have to be of the same type\.

Maps are ideal for storing JSON documents in DynamoDB\. The following example shows a map that contains a string, a number, and a nested list that contains another map\.

```
{
    Day: "Monday",
    UnreadEmails: 42,
    ItemsOnMyDesk: [
        "Coffee Cup",
        "Telephone",
        {
            Pens: { Quantity : 3},
            Pencils: { Quantity : 2},
            Erasers: { Quantity : 1}
        }
    ]
}
```

**Note**  
DynamoDB lets you work with individual elements within maps, even if those elements are deeply nested\. For more information, see [Using expressions in DynamoDB](Expressions.md)\.

### Sets<a name="HowItWorks.DataTypes.SetTypes"></a>

DynamoDB supports types that represent sets of number, string, or binary values\. All the elements within a set must be of the same type\. For example, an attribute of type Number Set can only contain numbers; String Set can only contain strings; and so on\.

There is no limit on the number of values in a set, as long as the item containing the values fits within the DynamoDB item size limit \(400 KB\)\.

Each value within a set must be unique\. The order of the values within a set is not preserved\. Therefore, your applications must not rely on any particular order of elements within the set\. DynamoDB does not support empty sets, however, empty string and binary values are allowed within a set\.

The following example shows a string set, a number set, and a binary set:

```
["Black", "Green", "Red"]

[42.2, -19, 7.5, 3.14]

["U3Vubnk=", "UmFpbnk=", "U25vd3k="]
```