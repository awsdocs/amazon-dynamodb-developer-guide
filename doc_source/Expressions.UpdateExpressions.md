# Update expressions<a name="Expressions.UpdateExpressions"></a>

To update an existing item in an Amazon DynamoDB table, you use the `UpdateItem` operation\. You must provide the key of the item that you want to update\. You must also provide an update expression, indicating the attributes that you want to modify and the values that you want to assign to them\. 

An *update expression* specifies how `UpdateItem` will modify the attributes of an item—for example, setting a scalar value or removing elements from a list or a map\.

The following is a syntax summary for update expressions\.

```
update-expression ::=
    [ SET action [, action] ... ]
    [ REMOVE action [, action] ...]
    [ ADD action [, action] ... ]
    [ DELETE action [, action] ...]
```

An update expression consists of one or more clauses\. Each clause begins with a `SET`, `REMOVE`, `ADD`, or `DELETE` keyword\. You can include any of these clauses in an update expression, in any order\. However, each action keyword can appear only once\.

Within each clause, there are one or more actions separated by commas\. Each action represents a data modification\.

The examples in this section are based on the `ProductCatalog` item shown in [Projection expressions](Expressions.ProjectionExpressions.md)\.

**Topics**
+ [SET—modifying or adding item attributes](#Expressions.UpdateExpressions.SET)
+ [REMOVE—deleting attributes from an item](#Expressions.UpdateExpressions.REMOVE)
+ [ADD—updating numbers and sets](#Expressions.UpdateExpressions.ADD)
+ [DELETE—removing elements from a set](#Expressions.UpdateExpressions.DELETE)

## SET—modifying or adding item attributes<a name="Expressions.UpdateExpressions.SET"></a>

Use the `SET` action in an update expression to add one or more attributes to an item\. If any of these attributes already exists, they are overwritten by the new values\. 

You can also use `SET` to add or subtract from an attribute that is of type `Number`\. To perform multiple `SET` actions, separate them with commas\.

In the following syntax summary:
+ The *path* element is the document path to the item\.
+ An **operand** element can be either a document path to an item or a function\.

```
set-action ::=
    path = value

value ::=
    operand
    | operand '+' operand
    | operand '-' operand

operand ::=
    path | function
```

The following `PutItem` operation creates a sample item that the examples refer to\.

```
aws dynamodb put-item \
    --table-name ProductCatalog \
    --item file://item.json
```

The arguments for `--item` are stored in the `item.json` file\. \(For simplicity, only a few item attributes are used\.\)

```
{
    "Id": {"N": "789"},
    "ProductCategory": {"S": "Home Improvement"},
    "Price": {"N": "52"},
    "InStock": {"BOOL": true},
    "Brand": {"S": "Acme"}
}
```

**Topics**
+ [Modifying attributes](#Expressions.UpdateExpressions.SET.ModifyingAttributes)
+ [Adding lists and maps](#Expressions.UpdateExpressions.SET.AddingListsAndMaps)
+ [Adding elements to a list](#Expressions.UpdateExpressions.SET.AddingListElements)
+ [Adding nested map attributes](#Expressions.UpdateExpressions.SET.AddingNestedMapAttributes)
+ [Incrementing and decrementing numeric attributes](#Expressions.UpdateExpressions.SET.IncrementAndDecrement)
+ [Appending elements to a list](#Expressions.UpdateExpressions.SET.UpdatingListElements)
+ [Preventing overwrites of an existing attribute](#Expressions.UpdateExpressions.SET.PreventingAttributeOverwrites)

### Modifying attributes<a name="Expressions.UpdateExpressions.SET.ModifyingAttributes"></a>

**Example**  
Update the `ProductCategory` and `Price` attributes\.  

```
aws dynamodb update-item \
    --table-name ProductCatalog \
    --key '{"Id":{"N":"789"}}' \
    --update-expression "SET ProductCategory = :c, Price = :p" \
    --expression-attribute-values file://values.json \
    --return-values ALL_NEW
```
The arguments for `--expression-attribute-values` are stored in the `values.json` file\.  

```
{
    ":c": { "S": "Hardware" },
    ":p": { "N": "60" }
}
```

**Note**  
In the `UpdateItem` operation, `--return-values ALL_NEW` causes DynamoDB to return the item as it appears after the update\.

### Adding lists and maps<a name="Expressions.UpdateExpressions.SET.AddingListsAndMaps"></a>

**Example**  
Add a new list and a new map\.  

```
aws dynamodb update-item \
    --table-name ProductCatalog \
    --key '{"Id":{"N":"789"}}' \
    --update-expression "SET RelatedItems = :ri, ProductReviews = :pr" \
    --expression-attribute-values file://values.json \
    --return-values ALL_NEW
```
The arguments for `--expression-attribute-values` are stored in the `values.json` file\.  

```
{
    ":ri": {
        "L": [
            { "S": "Hammer" }
        ]
    },
    ":pr": {
        "M": {
            "FiveStar": {
                "L": [
                    { "S": "Best product ever!" }
                ]
            }
        }
    }
}
```

### Adding elements to a list<a name="Expressions.UpdateExpressions.SET.AddingListElements"></a>

**Example**  
Add a new attribute to the `RelatedItems` list\. \(Remember that list elements are zero\-based, so \[0\] represents the first element in the list, \[1\] represents the second, and so on\.\)  

```
aws dynamodb update-item \
    --table-name ProductCatalog \
    --key '{"Id":{"N":"789"}}' \
    --update-expression "SET RelatedItems[1] = :ri" \
    --expression-attribute-values file://values.json \
    --return-values ALL_NEW
```
The arguments for `--expression-attribute-values` are stored in the `values.json` file\.  

```
{
    ":ri": { "S": "Nails" }
}
```

**Note**  
When you use `SET` to update a list element, the contents of that element are replaced with the new data that you specify\. If the element doesn't already exist, `SET` appends the new element at the end of the list\.  
If you add multiple elements in a single `SET` operation, the elements are sorted in order by element number\.

### Adding nested map attributes<a name="Expressions.UpdateExpressions.SET.AddingNestedMapAttributes"></a>

**Example**  
Add some nested map attributes\.  

```
aws dynamodb update-item \
    --table-name ProductCatalog \
    --key '{"Id":{"N":"789"}}' \
    --update-expression "SET #pr.#5star[1] = :r5, #pr.#3star = :r3" \
    --expression-attribute-names file://names.json \
    --expression-attribute-values file://values.json \
    --return-values ALL_NEW
```
The arguments for `--expression-attribute-names` are stored in the `names.json` file\.  

```
{
    "#pr": "ProductReviews",
    "#5star": "FiveStar",
    "#3star": "ThreeStar"
}
```
The arguments for `--expression-attribute-values` are stored in the `values.json` file\.  

```
{
    ":r5": { "S": "Very happy with my purchase" },
    ":r3": {
        "L": [
            { "S": "Just OK - not that great" }
        ]
    }
}
```

### Incrementing and decrementing numeric attributes<a name="Expressions.UpdateExpressions.SET.IncrementAndDecrement"></a>

You can add to or subtract from an existing numeric attribute\. To do this, use the `+` \(plus\) and `-` \(minus\) operators\.

**Example**  
Decrease the `Price` of an item\.  

```
aws dynamodb update-item \
    --table-name ProductCatalog \
    --key '{"Id":{"N":"789"}}' \
    --update-expression "SET Price = Price - :p" \
    --expression-attribute-values '{":p": {"N":"15"}}' \
    --return-values ALL_NEW
```
To increase the `Price`, you would use the `+` operator in the update expression\.

### Appending elements to a list<a name="Expressions.UpdateExpressions.SET.UpdatingListElements"></a>

You can add elements to the end of a list\. To do this, use `SET` with the `list_append` function\. \(The function name is case sensitive\.\) The `list_append` function is specific to the `SET` action and can only be used in an update expression\. The syntax is as follows\.
+ `list_append (list1, list2)`

The function takes two lists as input and appends all elements from `list2` to ` list1`\.

**Example**  
In [Adding elements to a list](#Expressions.UpdateExpressions.SET.AddingListElements), you create the `RelatedItems` list and populate it with two elements: `Hammer` and `Nails`\. Now you append two more elements to the end of `RelatedItems`\.  

```
aws dynamodb update-item \
    --table-name ProductCatalog \
    --key '{"Id":{"N":"789"}}' \
    --update-expression "SET #ri = list_append(#ri, :vals)" \
    --expression-attribute-names '{"#ri": "RelatedItems"}' \
    --expression-attribute-values file://values.json  \
    --return-values ALL_NEW
```
The arguments for `--expression-attribute-values` are stored in the `values.json` file\.  

```
{
    ":vals": {
        "L": [
            { "S": "Screwdriver" },
            {"S": "Hacksaw" }
        ]
    }
}
```
Finally, you append one more element to the *beginning* of `RelatedItems`\. To do this, swap the order of the `list_append` elements\. \(Remember that `list_append` takes two lists as input and appends the second list to the first\.\)  

```
aws dynamodb update-item \
    --table-name ProductCatalog \
    --key '{"Id":{"N":"789"}}' \
    --update-expression "SET #ri = list_append(:vals, #ri)" \
    --expression-attribute-names '{"#ri": "RelatedItems"}' \
    --expression-attribute-values '{":vals": {"L": [ { "S": "Chisel" }]}}' \
    --return-values ALL_NEW
```
The resulting `RelatedItems` attribute now contains five elements, in the following order: `Chisel`, `Hammer`, `Nails`, `Screwdriver`, `Hacksaw`\.

### Preventing overwrites of an existing attribute<a name="Expressions.UpdateExpressions.SET.PreventingAttributeOverwrites"></a>

If you want to avoid overwriting an existing attribute, you can use `SET` with the `if_not_exists` function\. \(The function name is case sensitive\.\) The `if_not_exists` function is specific to the `SET` action and can only be used in an update expression\. The syntax is as follows\.
+ `if_not_exists (path, value)`

If the item does not contain an attribute at the specified `path`, `if_not_exists` evaluates to `value`; otherwise, it evaluates to `path`\. 

**Example**  
Set the `Price` of an item, but only if the item does not already have a `Price` attribute\. \(If `Price` already exists, nothing happens\.\)  

```
aws dynamodb update-item \
    --table-name ProductCatalog \
    --key '{"Id":{"N":"789"}}' \
    --update-expression "SET Price = if_not_exists(Price, :p)" \
    --expression-attribute-values '{":p": {"N": "100"}}' \
    --return-values ALL_NEW
```

## REMOVE—deleting attributes from an item<a name="Expressions.UpdateExpressions.REMOVE"></a>

Use the `REMOVE` action in an update expression to remove one or more attributes from an item in Amazon DynamoDB\. To perform multiple `REMOVE` actions, separate them with commas\.

The following is a syntax summary for `REMOVE` in an update expression\. The only operand is the document path for the attribute that you want to remove\.

```
remove-action ::=
    path
```

**Example**  
Remove some attributes from an item\. \(If the attributes don't exist, nothing happens\.\)  

```
aws dynamodb update-item \
    --table-name ProductCatalog \
    --key '{"Id":{"N":"789"}}' \
    --update-expression "REMOVE Brand, InStock, QuantityOnHand" \
    --return-values ALL_NEW
```

### Removing elements from a list<a name="Expressions.UpdateExpressions.REMOVE.RemovingListElements"></a>

You can use `REMOVE` to delete individual elements from a list\.

**Example**  
In [Appending elements to a list](#Expressions.UpdateExpressions.SET.UpdatingListElements), you modify a list attribute \(`RelatedItems`\) so that it contained five elements:   
+ `[0]`—`Chisel`
+ `[1]`—`Hammer`
+ `[2]`—`Nails`
+ `[3]`—`Screwdriver`
+ `[4]`—`Hacksaw`
The following AWS Command Line Interface \(AWS CLI\) example deletes `Hammer` and `Nails` from the list\.  

```
aws dynamodb update-item \
    --table-name ProductCatalog \
    --key '{"Id":{"N":"789"}}' \
    --update-expression "REMOVE RelatedItems[1], RelatedItems[2]" \
    --return-values ALL_NEW
```
After `Hammer` and `Nails` are removed, the remaining elements are shifted\. The list now contains the following:  
+ `[0]`—`Chisel`
+ `[1]`—`Screwdriver`
+ `[2]`—`Hacksaw`

## ADD—updating numbers and sets<a name="Expressions.UpdateExpressions.ADD"></a>

**Note**  
In general, we recommend using `SET` rather than `ADD`\.

Use the `ADD` action in an update expression to add a new attribute and its values to an item\.

If the attribute already exists, the behavior of `ADD` depends on the attribute's data type:
+ If the attribute is a number, and the value you are adding is also a number, the value is mathematically added to the existing attribute\. \(If the value is a negative number, it is subtracted from the existing attribute\.\)
+ If the attribute is a set, and the value you are adding is also a set, the value is appended to the existing set\.

**Note**  
The `ADD` action supports only number and set data types\.

To perform multiple `ADD` actions, separate them with commas\.

In the following syntax summary:
+ The *path* element is the document path to an attribute\. The attribute must be either a `Number` or a set data type\. 
+ The *value* element is a number that you want to add to the attribute \(for `Number` data types\), or a set to append to the attribute \(for set types\)\.

```
add-action ::=
    path value
```

### Adding a number<a name="Expressions.UpdateExpressions.ADD.Number"></a>

Assume that the `QuantityOnHand` attribute does not exist\. The following AWS CLI example sets `QuantityOnHand` to 5\.

```
aws dynamodb update-item \
    --table-name ProductCatalog \
    --key '{"Id":{"N":"789"}}' \
    --update-expression "ADD QuantityOnHand :q" \
    --expression-attribute-values '{":q": {"N": "5"}}' \
    --return-values ALL_NEW
```

Now that `QuantityOnHand` exists, you can rerun the example to increment `QuantityOnHand` by 5 each time\.

### Adding elements to a set<a name="Expressions.UpdateExpressions.ADD.Set"></a>

Assume that the `Color` attribute does not exist\. The following AWS CLI example sets `Color` to a string set with two elements\.

```
aws dynamodb update-item \
    --table-name ProductCatalog \
    --key '{"Id":{"N":"789"}}' \
    --update-expression "ADD Color :c" \
    --expression-attribute-values '{":c": {"SS":["Orange", "Purple"]}}' \
    --return-values ALL_NEW
```

Now that `Color` exists, you can add more elements to it\.

```
aws dynamodb update-item \
    --table-name ProductCatalog \
    --key '{"Id":{"N":"789"}}' \
    --update-expression "ADD Color :c" \
    --expression-attribute-values '{":c": {"SS":["Yellow", "Green", "Blue"]}}' \
    --return-values ALL_NEW
```

## DELETE—removing elements from a set<a name="Expressions.UpdateExpressions.DELETE"></a>

**Important**  
The `DELETE` action supports only `Set` data types\.

Use the `DELETE` action in an update expression to remove one or more elements from a set\. To perform multiple `DELETE` actions, separate them with commas\.

In the following syntax summary:
+ The *path* element is the document path to an attribute\. The attribute must be a set data type\.
+ The *subset* is one or more elements that you want to delete from *path*\. You must specify *subset* as a set type\.

```
delete-action ::=
    path value
```

**Example**  
In [Adding elements to a set](#Expressions.UpdateExpressions.ADD.Set), you create the `Color` string set\. This example removes some of the elements from that set\.  

```
aws dynamodb update-item \
    --table-name ProductCatalog \
    --key '{"Id":{"N":"789"}}' \
    --update-expression "DELETE Color :p" \
    --expression-attribute-values '{":p": {"SS": ["Yellow", "Purple"]}}' \
    --return-values ALL_NEW
```