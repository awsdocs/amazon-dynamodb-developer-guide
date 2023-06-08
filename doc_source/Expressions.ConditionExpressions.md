# Condition expressions<a name="Expressions.ConditionExpressions"></a>

To manipulate data in an Amazon DynamoDB table, you use the `PutItem`, `UpdateItem`, and `DeleteItem` operations\. \(You can also use `BatchWriteItem` to perform multiple `PutItem` or `DeleteItem` operations in a single call\.\)

For these data manipulation operations, you can specify a *condition expression* to determine which items should be modified\. If the condition expression evaluates to true, the operation succeeds; otherwise, the operation fails\.

The following are some AWS Command Line Interface \(AWS CLI\) examples of using condition expressions\. These examples are based on the `ProductCatalog` table, which was introduced in [Specifying item attributes when using expressions](Expressions.Attributes.md)\. The partition key for this table is `Id`; there is no sort key\. The following `PutItem` operation creates a sample `ProductCatalog` item that the examples refer to\.

```
aws dynamodb put-item \
    --table-name ProductCatalog \
    --item file://item.json
```

The arguments for `--item` are stored in the `item.json` file\. \(For simplicity, only a few item attributes are used\.\)

```
{
    "Id": {"N": "456" },
    "ProductCategory": {"S": "Sporting Goods" },
    "Price": {"N": "650" }
}
```

**Topics**
+ [Conditional put](#Expressions.ConditionExpressions.PreventingOverwrites)
+ [Conditional deletes](#Expressions.ConditionExpressions.AdvancedComparisons)
+ [Conditional updates](#Expressions.ConditionExpressions.SimpleComparisons)
+ [Conditional expression examples](#Expressions.ConditionExpressions.ConditionalExamples)
+ [Comparison operator and function reference](Expressions.OperatorsAndFunctions.md)

## Conditional put<a name="Expressions.ConditionExpressions.PreventingOverwrites"></a>

The `PutItem` operation overwrites an item with the same key \(if it exists\)\. If you want to avoid this, use a condition expression\. This allows the write to proceed only if the item in question does not already have the same key\.

```
aws dynamodb put-item \
    --table-name ProductCatalog \
    --item file://item.json \
    --condition-expression "attribute_not_exists(Id)"
```

If the condition expression evaluates to false, DynamoDB returns the following error message: The conditional request failed\.

**Note**  
For more information about `attribute_not_exists` and other functions, see [Comparison operator and function reference](Expressions.OperatorsAndFunctions.md)\.

## Conditional deletes<a name="Expressions.ConditionExpressions.AdvancedComparisons"></a>

To perform a conditional delete, you use a `DeleteItem` operation with a condition expression\. The condition expression must evaluate to true in order for the operation to succeed; otherwise, the operation fails\.

Consider the item from [Condition expressions](#Expressions.ConditionExpressions)\.

```
{
    "Id": {
        "N": "456"
    },
    "Price": {
        "N": "650"
    },
    "ProductCategory": {
        "S": "Sporting Goods"
    }
}
```

Suppose that you wanted to delete the item, but only under the following conditions:
+  The `ProductCategory` is either "Sporting Goods" or "Gardening Supplies\."
+  The `Price` is between 500 and 600\.

The following example tries to delete the item\.

```
aws dynamodb delete-item \
    --table-name ProductCatalog \
    --key '{"Id":{"N":"456"}}' \
    --condition-expression "(ProductCategory IN (:cat1, :cat2)) and (Price between :lo and :hi)" \
    --expression-attribute-values file://values.json
```

The arguments for `--expression-attribute-values` are stored in the `values.json` file\.

```
{
    ":cat1": {"S": "Sporting Goods"},
    ":cat2": {"S": "Gardening Supplies"},
    ":lo": {"N": "500"},
    ":hi": {"N": "600"}
}
```

**Note**  
In the condition expression, the `:` \(colon character\) indicates an *expression attribute value*—a placeholder for an actual value\. For more information, see [Expression attribute values](Expressions.ExpressionAttributeValues.md)\.  
For more information about `IN`, `AND`, and other keywords, see [Comparison operator and function reference](Expressions.OperatorsAndFunctions.md)\.

In this example, the `ProductCategory` comparison evaluates to true, but the `Price` comparison evaluates to false\. This causes the condition expression to evaluate to false and the `DeleteItem` operation to fail\.

## Conditional updates<a name="Expressions.ConditionExpressions.SimpleComparisons"></a>

To perform a conditional update, you use an `UpdateItem` operation with a condition expression\. The condition expression must evaluate to true in order for the operation to succeed; otherwise, the operation fails\.

**Note**  
`UpdateItem` also supports *update expressions*, where you specify the modifications you want to make to an item\. For more information, see [Update expressions](Expressions.UpdateExpressions.md)\.

Suppose that you started with the item shown in [Condition expressions](#Expressions.ConditionExpressions)\.

```
{
    "Id": { "N": "456"},
    "Price": {"N": "650"},
    "ProductCategory": {"S": "Sporting Goods"}
}
```

The following example performs an `UpdateItem` operation\. It tries to reduce the `Price` of a product by 75—but the condition expression prevents the update if the current `Price` is less than or equal to 500\.

```
aws dynamodb update-item \
    --table-name ProductCatalog \
    --key '{"Id": {"N": "456"}}' \
    --update-expression "SET Price = Price - :discount" \
    --condition-expression "Price > :limit" \
    --expression-attribute-values file://values.json
```

The arguments for `--expression-attribute-values` are stored in the `values.json` file\.

```
{
    ":discount": { "N": "75"},
    ":limit": {"N": "500"}
}
```

If the starting `Price` is 650, the `UpdateItem` operation reduces the `Price` to 575\. If you run the `UpdateItem` operation again, the `Price` is reduced to 500\. If you run it a third time, the condition expression evaluates to false, and the update fails\.

**Note**  
In the condition expression, the `:` \(colon character\) indicates an *expression attribute value*—a placeholder for an actual value\. For more information, see [Expression attribute values](Expressions.ExpressionAttributeValues.md)\.  
For more information about "*>*" and other operators, see [Comparison operator and function reference](Expressions.OperatorsAndFunctions.md)\.

## Conditional expression examples<a name="Expressions.ConditionExpressions.ConditionalExamples"></a>

For more information about the functions used in the following examples, see [Comparison operator and function reference](Expressions.OperatorsAndFunctions.md)\. If you want to know more about how to specify different attribute types in an expression, see [Specifying item attributes when using expressions](Expressions.Attributes.md)\. 

### Checking for attributes in an item<a name="Expressions.ConditionExpressions.CheckingForAttributes"></a>

You can check for the existence \(or nonexistence\) of any attribute\. If the condition expression evaluates to true, the operation succeeds; otherwise, it fails\.

The following example uses `attribute_not_exists` to delete a product only if it does not have a `Price` attribute\.

```
aws dynamodb delete-item \
    --table-name ProductCatalog \
    --key '{"Id": {"N": "456"}}' \
    --condition-expression "attribute_not_exists(Price)"
```

DynamoDB also provides an `attribute_exists` function\. The following example deletes a product only if it has received poor reviews\.

```
aws dynamodb delete-item \
    --table-name ProductCatalog \
    --key '{"Id": {"N": "456"}}' \
    --condition-expression "attribute_exists(ProductReviews.OneStar)"
```

### Checking for attribute type<a name="Expressions.ConditionExpressions.CheckingForAttributeType"></a>

You can check the data type of an attribute value by using the `attribute_type` function\. If the condition expression evaluates to true, the operation succeeds; otherwise, it fails\.

The following example uses `attribute_type` to delete a product only if it has a `Color` attribute of type String Set\. 

```
aws dynamodb delete-item \
    --table-name ProductCatalog \
    --key '{"Id": {"N": "456"}}' \
    --condition-expression "attribute_type(Color, :v_sub)" \
    --expression-attribute-values file://expression-attribute-values.json
```

The arguments for `--expression-attribute-values` are stored in the expression\-attribute\-values\.json file\.

```
{
    ":v_sub":{"S":"SS"}
}
```

### Checking string starting value<a name="Expressions.ConditionExpressions.CheckingBeginsWith"></a>

You can check if a String attribute value begins with a particular substring by using the `begins_with` function\. If the condition expression evaluates to true, the operation succeeds; otherwise, it fails\. 

The following example uses `begins_with` to delete a product only if the `FrontView` element of the `Pictures` map starts with a specific value\.

```
aws dynamodb delete-item \
    --table-name ProductCatalog \
    --key '{"Id": {"N": "456"}}' \
    --condition-expression "begins_with(Pictures.FrontView, :v_sub)" \
    --expression-attribute-values file://expression-attribute-values.json
```

The arguments for `--expression-attribute-values` are stored in the expression\-attribute\-values\.json file\.

```
{
    ":v_sub":{"S":"http://"}
}
```

### Checking for an element in a set<a name="Expressions.ConditionExpressions.CheckingForContains"></a>

You can check for an element in a set or look for a substring within a string by using the `contains` function\. If the condition expression evaluates to true, the operation succeeds; otherwise, it fails\. 

The following example uses `contains` to delete a product only if the `Color` String Set has an element with a specific value\. 

```
aws dynamodb delete-item \
    --table-name ProductCatalog \
    --key '{"Id": {"N": "456"}}' \
    --condition-expression "contains(Color, :v_sub)" \
    --expression-attribute-values file://expression-attribute-values.json
```

The arguments for `--expression-attribute-values` are stored in the expression\-attribute\-values\.json file\.

```
{
    ":v_sub":{"S":"Red"}
}
```

### Checking the size of an attribute value<a name="Expressions.ConditionExpressions.CheckingForSize"></a>

You can check for the size of an attribute value by using the `size` function\. If the condition expression evaluates to true, the operation succeeds; otherwise, it fails\. 

The following example uses `size` to delete a product only if the size of the `VideoClip` Binary attribute is greater than `64000` bytes\. 

```
aws dynamodb delete-item \
    --table-name ProductCatalog \
    --key '{"Id": {"N": "456"}}' \
    --condition-expression "size(VideoClip) > :v_sub" \
    --expression-attribute-values file://expression-attribute-values.json
```

The arguments for `--expression-attribute-values` are stored in the expression\-attribute\-values\.json file\.

```
{
    ":v_sub":{"N":"64000"}
}
```