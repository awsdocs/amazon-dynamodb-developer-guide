# Writing Conditions With Legacy Parameters<a name="LegacyConditionalParameters.Conditions"></a>

The following section describes how to write conditions for use with legacy parameters, such as `Expected`, `QueryFilter`, and `ScanFilter`\.

**Note**  
New applications should use expression parameters instead\. For more information, see [Using Expressions in DynamoDB](Expressions.md)\.

## Simple Conditions<a name="LegacyConditionalParameters.Simple"></a>

With attribute values, you can write conditions for comparisons against table attributes\. A condition always evaluates to true or false, and consists of:
+ `ComparisonOperator` — greater than, less than, equal to, and so on\.
+ `AttributeValueList` \(optional\) — attribute value\(s\) to compare against\. Depending on the `ComparisonOperator` being used, the `AttributeValueList` might contain one, two, or more values; or it might not be present at all\.

The following sections describe the various comparison operators, along with examples of how to use them in conditions\.

### Comparison Operators with No Attribute Values<a name="LegacyConditionalParameters.Conditions.Simple.NoAttributeValues"></a>
+ `NOT_NULL` \- true if an attribute exists\.
+ `NULL` \- true if an attribute does not exist\.

Use these operators to check whether an attribute exists, or doesn't exist\. Because there is no value to compare against, do not specify `AttributeValueList`\.

**Example**

The following expression evaluates to true if the *Dimensions* attribute exists\.

```
...
    "Dimensions": {
         ComparisonOperator: "NOT_NULL"
    }
...
```

### Comparison Operators with One Attribute Value<a name="LegacyConditionalParameters.Conditions.Simple.OneAttributeValue"></a>
+ `EQ` \- true if an attribute is equal to a value\.

  `AttributeValueList` can contain only one value of type String, Number, Binary, String Set, Number Set, or Binary Set\. If an item contains a value of a different type than the one specified in the request, the value does not match\. For example, the string `"3"` is not equal to the number `3`\. Also, the number `3` is not equal to the number set `[3, 2, 1]`\. 
+ `NE` \- true if an attribute is not equal to a value\.

  `AttributeValueList` can contain only one value of type String, Number, Binary, String Set, Number Set, or Binary Set\. If an item contains a value of a different type than the one specified in the request, the value does not match\.
+ `LE` \- true if an attribute is less than or equal to a value\.

  `AttributeValueList` can contain only one value of type String, Number, or Binary \(not a set\)\. If an item contains an `AttributeValue` of a different type than the one specified in the request, the value does not match\.
+ `LT` \- true if an attribute is less than a value\.

  `AttributeValueList` can contain only one value of type String, Number, or Binary \(not a set\)\. If an item contains a value of a different type than the one specified in the request, the value does not match\.
+ `GE` \- true if an attribute is greater than or equal to a value\.

  `AttributeValueList` can contain only one value of type String, Number, or Binary \(not a set\)\. If an item contains a value of a different type than the one specified in the request, the value does not match\.
+ `GT` \- true if an attribute is greater than a value\.

  `AttributeValueList` can contain only one value of type String, Number, or Binary \(not a set\)\. If an item contains a value of a different type than the one specified in the request, the value does not match\.
+ `CONTAINS` \- true if a value is present within a set, or if one value contains another\.

  `AttributeValueList` can contain only one value of type String, Number, or Binary \(not a set\)\. If the target attribute of the comparison is a String, then the operator checks for a substring match\. If the target attribute of the comparison is Binary, then the operator looks for a subsequence of the target that matches the input\. If the target attribute of the comparison is a set, then the operator evaluates to true if it finds an exact match with any member of the set\.
+ `NOT_CONTAINS` \- true if a value is *not* present within a set, or if one value does not contain another value\.

  `AttributeValueList` can contain only one value of type String, Number, or Binary \(not a set\)\. If the target attribute of the comparison is a String, then the operator checks for the absence of a substring match\. If the target attribute of the comparison is Binary, then the operator checks for the absence of a subsequence of the target that matches the input\. If the target attribute of the comparison is a set, then the operator evaluates to true if it *does not* find an exact match with any member of the set\.
+ `BEGINS_WITH` \- true if the first few characters of an attribute match the provided value\. Do not use this operator for comparing numbers\.

  `AttributeValueList` can contain only one value of type String or Binary \(not a Number or a set\)\. The target attribute of the comparison must be a String or Binary \(not a Number or a set\)\.

Use these operators to compare an attribute with a value\. You must specify an `AttributeValueList` consisting of a single value\. For most of the operators, this value must be a scalar; however, the `EQ` and `NE` operators also support sets\.

**Examples**

The following expressions evaluate to true if: 
+ A product's price is greater than 100\.

  ```
  ...
      "Price": {
          ComparisonOperator: "GT",
          AttributeValueList: [ {"N":"100"} ]
      }
  ...
  ```
+ A product category begins with "Bo"\.

  ```
  ...
      "ProductCategory": {
          ComparisonOperator: "BEGINS_WITH",
          AttributeValueList: [ {"S":"Bo"} ]
      }
  ...
  ```
+ A product is available in either red, green, or black:

  ```
  ...
      "Color": {
          ComparisonOperator: "EQ",
          AttributeValueList: [
              [ {"S":"Black"}, {"S":"Red"}, {"S":"Green"} ]
          ]
      }
  ...
  ```
**Note**  
When comparing set data types, the order of the elements does not matter\. DynamoDB will return only the items with the same set of values, regardless of the order in which you specify them in your request\.

### Comparison Operators with Two Attribute Values<a name="LegacyConditionalParameters.Conditions.Simple.TwoAttributeValues"></a>
+ `BETWEEN` \- true if a value is between a lower bound and an upper bound, endpoints inclusive\.

  `AttributeValueList` must contain two elements of the same type, either String, Number, or Binary \(not a set\)\. A target attribute matches if the target value is greater than, or equal to, the first element and less than, or equal to, the second element\. If an item contains a value of a different type than the one specified in the request, the value does not match\.

Use this operator to determine if an attribute value is within a range\. The `AttributeValueList` must contain two scalar elements of the same type \- String, Number, or Binary\.

 **Example**

The following expression evaluates to true if a product's price is between 100 and 200\.

```
...
    "Price": {
        ComparisonOperator: "BETWEEN",
        AttributeValueList: [ {"N":"100"}, {"N":"200"} ]
    }
...
```

### Comparison Operators with *N* Attribute Values<a name="LegacyConditionalParameters.Conditions.Simple.NAttributeValues"></a>
+ `IN` \- true if a value is equal to any of the values in an enumerated list\. Only scalar values are supported in the list, not sets\. The target attribute must be of the same type and exact value in order to match\.

  `AttributeValueList` can contain one or more elements of type String, Number, or Binary \(not a set\)\. These attributes are compared against an existing non\-set type attribute of an item\. If *any* elements of the input set are present in the item attribute, the expression evaluates to true\.

  `AttributeValueList` can contain one or more values of type String, Number, or Binary \(not a set\)\. The target attribute of the comparison must be of the same type and exact value to match\. A String never matches a String set\.

Use this operator to determine whether the supplied value is within an enumerated list\. You can specify any number of scalar values in `AttributeValueList`, but they all must be of the same data type\.

 **Example**

The following expression evaluates to true if the value for *Id* is 201, 203, or 205\.

```
...
    "Id": {
        ComparisonOperator: "IN",
        AttributeValueList: [ {"N":"201"}, {"N":"203"}, {"N":"205"} ]
    }
...
```

## Using Multiple Conditions<a name="LegacyConditionalParameters.Conditions.Multiple"></a>

DynamoDB lets you combine multiple conditions to form complex expressions\. You do this by providing at least two expressions, with an optional [ConditionalOperator](LegacyConditionalParameters.ConditionalOperator.md)\.

By default, when you specify more than one condition, *all* of the conditions must evaluate to true in order for the entire expression to evaluate to true\. In other words, an implicit *AND* operation takes place\.

 **Example**

The following expression evaluates to true if a product is a book which has at least 600 pages\. Both of the conditions must evaluate to true, since they are implicitly *AND*ed together\.

```
... 
    "ProductCategory": { 
        ComparisonOperator: "EQ",
        AttributeValueList: [ {"S":"Book"} ]
    },
    "PageCount": { 
        ComparisonOperator: "GE",
        AttributeValueList: [ {"N":600"} ]
    }
...
```

You can use [ConditionalOperator](LegacyConditionalParameters.ConditionalOperator.md) to clarify that an *AND* operation will take place\. The following example behaves in the same manner as the previous one\.

```
...   
    "ConditionalOperator" : "AND",
    "ProductCategory": { 
        "ComparisonOperator": "EQ",
        "AttributeValueList": [ {"N":"Book"} ]
    },
    "PageCount": { 
        "ComparisonOperator": "GE",
        "AttributeValueList": [ {"N":600"} ]
    }
...
```

You can also set `ConditionalOperator` to *OR*, which means that *at least one* of the conditions must evaluate to true\.

 **Example**

 The following expression evaluates to true if a product is a mountain bike, if it is a particular brand name, or if its price is greater than 100\.

```
... 
    ConditionalOperator : "OR",
    "BicycleType": { 
        "ComparisonOperator": "EQ",
        "AttributeValueList": [ {"S":"Mountain" ]
    },
    "Brand": { 
        "ComparisonOperator": "EQ",
        "AttributeValueList": [ {"S":"Brand-Company A" ]
    },
    "Price": { 
        "ComparisonOperator": "GT",
        "AttributeValueList": [ {"N":"100"} ]
    }
...
```

**Note**  
In a complex expression, the conditions are processed in order, from the first condition to the last\.  
You cannot use both AND and OR in a single expression\.

## Other Conditional Operators<a name="LegacyConditionalParameters.Conditions.Other"></a>

In previous releases of DynamoDB, the `Expected` parameter behaved differently for conditional writes\. Each item in the `Expected` map represented an attribute name for DynamoDB to check, along with the following:
+ `Value` — a value to compare against the attribute\.
+ `Exists` — determine whether the value exists prior to attempting the operation\.

The `Value` and `Exists` options continue to be supported in DynamoDB; however, they only let you test for an equality condition, or whether an attribute exists\. We recommend that you use `ComparisonOperator` and `AttributeValueList` instead, because these options let you construct a much wider range of conditions\.

**Example**  
A `DeleteItem` can check to see whether a book is no longer in publication, and only delete it if this condition is true\. Here is an AWS CLI example using a legacy condition:  

```
aws dynamodb delete-item \
    --table-name ProductCatalog \
    --key '{
        "Id": {"N":"600"}
    }' \
    --expected '{
        "InPublication": { 
            "Exists": true, 
            "Value": {"BOOL":false} 
        } 
    }'
```
The following example does the same thing, but does not use a legacy condition:  

```
aws dynamodb delete-item \
    --table-name ProductCatalog \
    --key '{
        "Id": {"N":"600"}
    }' \
    --expected '{
        "InPublication": { 
            "ComparisonOperator": "EQ", 
            "AttributeValueList": [ {"BOOL":false} ]
        } 
    }'
```

**Example**  
A `PutItem` operation can protect against overwriting an existing item with the same primary key attributes\. Here is an example using a legacy condition:  

```
aws dynamodb put-item \
    --table-name ProductCatalog \
    --item '{
      "Id": {"N":"500"},
        "Title": {"S":"Book 500 Title"}
    }' \
    --expected '{
        "Id": { "Exists": false } 
    }'
```
The following example does the same thing, but does not use a legacy condition:  

```
aws dynamodb put-item \
    --table-name ProductCatalog \
    --item '{
      "Id": {"N":"500"},
        "Title": {"S":"Book 500 Title"}
    }' \
    --expected '{
        "Id": { "ComparisonOperator": "NULL" } 
    }'
```

**Note**  
For conditions in the `Expected` map, do not use the legacy `Value` and `Exists` options together with `ComparisonOperator` and `AttributeValueList`\. If you do this, your conditional write will fail\.