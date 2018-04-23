# Update Expressions<a name="Expressions.UpdateExpressions"></a>

To update an existing item in a table, you use the `UpdateItem` operation\. You must provide the key of the item you want to update\. You must also provide an update expression, indicating the attributes you want to modify and the values you want to assign to them\. 

An *update expression* specifies how `UpdateItem` will modify the attributes of an item—for example, setting a scalar value, or removing elements from a list or a map\.

The following is a syntax summary for update expressions:

```
update-expression ::=
    [ SET action [, action] ... ] 
    [ REMOVE action [, action] ...] 
    [ ADD action [, action] ... ] 
    [ DELETE action [, action] ...]
```

An update expression consists of one or more clauses\. Each clause begins with a `SET`, `REMOVE`, `ADD` or `DELETE` keyword\. You can include any of these clauses in an update expression, in any order\. However, each action keyword can appear only once\.

Within each clause are one or more actions, separated by commas\. Each action represents a data modification\.

The examples in this section are based on the *ProductCatalog* item shown in [Projection Expressions](Expressions.ProjectionExpressions.md)\.

**Topics**
+ [*SET*—Modifying or Adding Item Attributes](#Expressions.UpdateExpressions.SET)
+ [*REMOVE*—Deleting Attributes From An Item](#Expressions.UpdateExpressions.REMOVE)
+ [*ADD*—Updating Numbers and Sets](#Expressions.UpdateExpressions.ADD)
+ [*DELETE*—Removing Elements From A Set](#Expressions.UpdateExpressions.DELETE)

## *SET*—Modifying or Adding Item Attributes<a name="Expressions.UpdateExpressions.SET"></a>

Use the `SET` action in an update expression to add one or more attributes to an item\. If any of these attribute already exist, they are overwritten by the new values\. 

You can also use `SET` to add or subtract from an attribute that is of type Number\. To perform multiple `SET` actions, separate them by commas\.

In the following syntax summary:
+ The *path* element is the document path to the item\.
+ An **operand** element can be either a document path to an item, or a function\.

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

The following `PutItem` operation creates a sample item that we will refer to in the examples:

```
aws dynamodb put-item \
    --table-name ProductCatalog \
    --item file://item.json
```

The arguments for `--item` are stored in the file `item.json`\. \(For simplicity, only a few item attributes are used\.\)

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
+ [Modifying Attributes](#Expressions.UpdateExpressions.SET.ModifyingAttributes)
+ [Adding Lists and Maps](#Expressions.UpdateExpressions.SET.AddingListsAndMaps)
+ [Adding Elements To a List](#Expressions.UpdateExpressions.SET.AddingListElements)
+ [Adding Nested Map Attributes](#Expressions.UpdateExpressions.SET.AddingNestedMapAttributes)
+ [Incrementing and Decrementing Numeric Attributes](#Expressions.UpdateExpressions.SET.IncrementAndDecrement)
+ [Appending Elements To a List](#Expressions.UpdateExpressions.SET.UpdatingListElements)
+ [Preventing Overwrites of an Existing Attribute](#Expressions.UpdateExpressions.SET.PreventingAttributeOverwrites)

### Modifying Attributes<a name="Expressions.UpdateExpressions.SET.ModifyingAttributes"></a>

**Example**  
Update the *ProductCategory* and *Price* attributes:  

```
aws dynamodb update-item \
    --table-name ProductCatalog \
    --key '{"Id":{"N":"789"}}' \
    --update-expression "SET ProductCategory = :c, Price = :p" \
    --expression-attribute-values file://values.json \
    --return-values ALL_NEW
```
The arguments for `--expression-attribute-values` are stored in the file `values.json`:  

```
{
    ":c": { "S": "Hardware" },
    ":p": { "N": "60" }
}
```

**Note**  
In the `UpdateItem` operation, `--return-values ALL_NEW` causes DynamoDB to return the item as it appears after the update\.

### Adding Lists and Maps<a name="Expressions.UpdateExpressions.SET.AddingListsAndMaps"></a>

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
The arguments for `--expression-attribute-values` are stored in the file `values.json`:  

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

### Adding Elements To a List<a name="Expressions.UpdateExpressions.SET.AddingListElements"></a>

**Example**  
Add a new attribute to the *RelatedItems* list\. \(Remember that list elements are zero\-based, so \[0\] represents the first element in the list, \[1\] represents the second, and so on\.\)  

```
aws dynamodb update-item \
    --table-name ProductCatalog \
    --key '{"Id":{"N":"789"}}' \
    --update-expression "SET RelatedItems[1] = :ri" \
    --expression-attribute-values file://values.json \
    --return-values ALL_NEW
```
The arguments for `--expression-attribute-values` are stored in the file `values.json`:  

```
{
    ":ri": { "S": "Nails" }
}
```

**Note**  
When you use `SET` to update a list element, the contents of that element are replaced with the new data that you specify\. If the element does not already exist, `SET` will append the new element at the end of the list\.  
If you add multiple elements in a single `SET` operation, the elements are sorted in order by element number\.

### Adding Nested Map Attributes<a name="Expressions.UpdateExpressions.SET.AddingNestedMapAttributes"></a>

**Example**  
Add some nested map attributes:  

```
aws dynamodb update-item \
    --table-name ProductCatalog \
    --key '{"Id":{"N":"789"}}' \
    --update-expression "SET #pr.#5star[1] = :r5, #pr.#3star = :r3" \
    --expression-attribute-names file://names.json \
    --expression-attribute-values file://values.json \
    --return-values ALL_NEW
```
The arguments for `--expression-attribute-names` are stored in the file `names.json`:  

```
{
    "#pr": "ProductReviews",
    "#5star": "FiveStar",
    "#3star": "ThreeStar"
}
```
The arguments for `--expression-attribute-values` are stored in the file `values.json`:  

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

### Incrementing and Decrementing Numeric Attributes<a name="Expressions.UpdateExpressions.SET.IncrementAndDecrement"></a>

You can add to or subtract from an existing numeric attribute\. To do this, use the `+` \(plus\) and `-` \(minus\) operators\.

**Example**  
Decrease the *Price* of an item:  

```
aws dynamodb update-item \
    --table-name ProductCatalog \
    --key '{"Id":{"N":"789"}}' \
    --update-expression "SET Price = Price - :p" \
    --expression-attribute-values '{":p": {"N":"15"}}' \
    --return-values ALL_NEW
```
To increase the *Price*, you would use the `+` operator in the update expression\.

### Appending Elements To a List<a name="Expressions.UpdateExpressions.SET.UpdatingListElements"></a>

You can add elements to the end of a list,\. To do this, use `SET` with the `list_append` function\. \(The function name is case\-sensitive\.\) The `list_append` function is specific to the `SET` action, and can only be used in an update expression\. The syntax is:
+ `list_append (list1, list2)`

The function takes two lists as input, and appends `list2` to ` list1`\.

**Example**  
In [Adding Elements To a List](#Expressions.UpdateExpressions.SET.AddingListElements), we created the `RelatedItems` list and populated it with two elements: `Hammer` and `Nails`\. Now we will append two more elements to the end of `RelatedItems`:  

```
aws dynamodb update-item \
    --table-name ProductCatalog \
    --key '{"Id":{"N":"789"}}' \
    --update-expression "SET #ri = list_append(#ri, :vals)" \
    --expression-attribute-names '{"#ri": "RelatedItems"}' \
    --expression-attribute-values file://values.json  \
    --return-values ALL_NEW
```
The arguments for `--expression-attribute-values` are stored in the file `values.json`:  

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
Finally, we will append one more element to the *beginning* of `RelatedItems`\. To do this, we will swap the order of the list\_append elements\. \(Remember that `list_append` takes two lists as input, and appends the second list to the first\.\)  

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

### Preventing Overwrites of an Existing Attribute<a name="Expressions.UpdateExpressions.SET.PreventingAttributeOverwrites"></a>

If you want to avoid overwriting an existing attribute, you can use `SET` with the `if_not_exists` function\. \(The function name is case\-sensitive\.\) The `if_not_exists` function is specific to the `SET` action, and can only be used in an update expression\. The syntax is:
+ `if_not_exists (path, value)`

If the item does not contain an attribute at the specified `path`, then `if_not_exists` evaluates to `value`; otherwise, it evaluates to `path`\. 

**Example**  
Set the *Price* of an item, but only if the item does not already have a *Price* attribute\. \(If *Price* already exists, nothing happens\.\)  

```
aws dynamodb update-item \
    --table-name ProductCatalog \
    --key '{"Id":{"N":"789"}}' \
    --update-expression "SET Price = if_not_exists(Price, :p)" \
    --expression-attribute-values '{":p": {"N": "100"}}' \
    --return-values ALL_NEW
```

## *REMOVE*—Deleting Attributes From An Item<a name="Expressions.UpdateExpressions.REMOVE"></a>

Use the `REMOVE` action in an update expression to remove one or more attributes from an item\. To perform multiple `REMOVE` actions, separate them by commas\.

The following is a syntax summary for `REMOVE` in an update expression\. The only operand is the document path for the attribute you want to remove:

```
remove-action ::=
    path
```

**Example**  
Remove some attributes from an item\. \(If the attributes do not exist, nothing happens\.\)  

```
aws dynamodb update-item \
    --table-name ProductCatalog \
    --key '{"Id":{"N":"789"}}' \
    --update-expression "REMOVE Brand, InStock, QuantityOnHand" \
    --return-values ALL_NEW
```

### Removing Elements From a List<a name="Expressions.UpdateExpressions.REMOVE.RemovingListElements"></a>

You can use `REMOVE` to delete individual elements from a list\.

**Example**  
In [Appending Elements To a List](#Expressions.UpdateExpressions.SET.UpdatingListElements), we modified a list attribute \(`RelatedItems`\) so that it contained five elements:   
+ `[0]`—`Chisel`
+ `[1]`—`Hammer`
+ `[2]`—`Nails`
+ `[3]`—`Screwdriver`
+ `[4]`—`Hacksaw`
The following AWS CLI example deletes `Hammer` and `Nails` from the list\.  

```
aws dynamodb update-item \
    --table-name ProductCatalog \
    --key '{"Id":{"N":"789"}}' \
    --update-expression "REMOVE RelatedItems[1], RelatedItems[2]" \
    --return-values ALL_NEW
```
After removing `Hammer` and `Nails`, the remaining elements are shifted\. The list now contains the following:  
+ `[0]`—`Chisel`
+ `[1]`—`Screwdriver`
+ `[2]`—`Hacksaw`

## *ADD*—Updating Numbers and Sets<a name="Expressions.UpdateExpressions.ADD"></a>

**Note**  
In general, we recommend using `SET` rather than `ADD`\.

Use the `ADD` action in an update expression to add a new attribute and its value\(s\) to an item\.

If the attribute already exists, then the behavior of `ADD` depends on the attribute's data type:
+ If the attribute is a number, and the value you are adding is also a number, then the value is mathematically added to the existing attribute\. \(If the value is a negative number, then it is subtracted from the existing attribute\.\)
+ If the attribute is a set, and the value you are adding is also a set, then the value is appended to the existing set\.

**Note**  
The `ADD` action only supports number and set data types\.

To perform multiple `ADD` actions, separate them by commas\.

In the following syntax summary:
+ The *path* element is the document path to an attribute\. The attribute must be either a Number or a set data type\. 
+ The *value* element is a number that you want to add to the attribute \(for Number data types\), or a set to append to the attribute \(for set types\)\.

```
add-action ::=
    path value
```

### Adding a Number<a name="Expressions.UpdateExpressions.ADD.Number"></a>

Assume that the `QuantityOnHand` attribute does not exist\. The following AWS CLI example sets `QuantityOnHand` to 5:

```
aws dynamodb update-item \
    --table-name ProductCatalog \
    --key '{"Id":{"N":"789"}}' \
    --update-expression "ADD QuantityOnHand :q" \
    --expression-attribute-values '{":q": {"N": "5"}}' \
    --return-values ALL_NEW
```

Now that `QuantityOnHand` exists, you can re\-run the example to increment `QuantityOnHand` by 5 each time\.

### Adding Elements To A Set<a name="Expressions.UpdateExpressions.ADD.Set"></a>

Assume that the `Color` attribute does not exist\. The following AWS CLI example sets `Color` to a string set with two elements:

```
aws dynamodb update-item \
    --table-name ProductCatalog \
    --key '{"Id":{"N":"789"}}' \
    --update-expression "ADD Color :c" \
    --expression-attribute-values '{":c": {"SS":["Orange", "Purple"]}}' \
    --return-values ALL_NEW
```

Now that `Color` exists, we can add more elements to it:

```
aws dynamodb update-item \
    --table-name ProductCatalog \
    --key '{"Id":{"N":"789"}}' \
    --update-expression "ADD Color :c" \
    --expression-attribute-values '{":c": {"SS":["Yellow", "Green", "Blue"]}}' \
    --return-values ALL_NEW
```

## *DELETE*—Removing Elements From A Set<a name="Expressions.UpdateExpressions.DELETE"></a>

**Important**  
The `DELETE` action only supports Set data types\.

Use the `DELETE` action in an update expression to remove one or more elements from a set\. To perform multiple `DELETE` actions, separate them by commas\.

In the following syntax summary:
+ The *path* element is the document path to an attribute\. The attribute must be a set data type\.
+ The *subset* is one or more elements that you want to delete from *path*\. that you want to delete\. You must specify *subset* as a set type\.

```
delete-action ::=
    path value
```

**Example**  
In [Adding Elements To A Set](#Expressions.UpdateExpressions.ADD.Set), we created the `Colors` string set\. This example removes some of the elements from that set:  

```
aws dynamodb update-item \
    --table-name ProductCatalog \
    --key '{"Id":{"N":"789"}}' \
    --update-expression "DELETE Color :p" \
    --expression-attribute-values '{":p": {"SS": ["Yellow", "Purple"]}}' \
    --return-values ALL_NEW
```