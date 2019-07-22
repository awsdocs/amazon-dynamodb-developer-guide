# Condition Expressions<a name="Expressions.ConditionExpressions"></a>

To manipulate data in a DynamoDB table, you use the `PutItem`, `UpdateItem` and `DeleteItem` operations\. \(You can also use `BatchWriteItem` to perform multiple `PutItem` or `DeleteItem` operations in a single call\.\)

For these data manipulation operations, you can specify a *condition expression* to determine which items should be modified\. If the condition expression evaluates to true, the operation succeeds; otherwise, the operation fails\.

The following are some AWS CLI examples of using condition expressions\. These examples are based on the *ProductCatalog* table, which was introduced in [Specifying Item Attributes](Expressions.Attributes.md)\. The partition key for this table is `Id`; there is no sort key\. The following `PutItem` operation creates a sample *ProductCatalog* item that we will refer to in the examples:

```
aws dynamodb put-item \
    --table-name ProductCatalog \
    --item file://item.json
```

The arguments for `--item` are stored in the file `item.json`\. \(For simplicity, only a few item attributes are used\.\)

```
{
    "Id": {"N": "456" },
    "ProductCategory": {"S": "Sporting Goods" },
    "Price": {"N": "650" }
}
```

**Topics**
+ [Preventing Overwrites of an Existing Item](#Expressions.ConditionExpressions.PreventingOverwrites)
+ [Checking for Attributes in an Item](#Expressions.ConditionExpressions.CheckingForAttributes)
+ [Conditional Deletes](#Expressions.ConditionExpressions.AdvancedComparisons)
+ [Conditional Updates](#Expressions.ConditionExpressions.SimpleComparisons)
+ [Comparison Operator and Function Reference](Expressions.OperatorsAndFunctions.md)

## Preventing Overwrites of an Existing Item<a name="Expressions.ConditionExpressions.PreventingOverwrites"></a>

The `PutItem` operation will overwrite an item with the same key \(if it exists\)\. If you want to avoid this, use a condition expression\. This will allow the write to proceed only if the item in question does not already have the same key:

```
aws dynamodb put-item \
    --table-name ProductCatalog \
    --item file://item.json \
    --condition-expression "attribute_not_exists(Id)"
```

If the condition expression evaluates to false, DynamoDB returns the following error message: *The conditional request failed*

**Note**  
For more information about `attribute_not_exists` and other functions, see [Comparison Operator and Function Reference](Expressions.OperatorsAndFunctions.md)\.

## Checking for Attributes in an Item<a name="Expressions.ConditionExpressions.CheckingForAttributes"></a>

You can check for the existence \(or nonexistence\) of any attribute\. If the condition expression evaluates to true, the operation will succeed; otherwise, it will fail\.

The following example uses `attribute_not_exists` to delete a product only if it does not have a `Price` attribute:

```
aws dynamodb delete-item \
    --table-name ProductCatalog \
    --key '{"Id": {"N": "456"}}' \
    --condition-expression "attribute_not_exists(Price)"
```

DynamoDB also provides an `attribute_exists` function\. The following example will delete a product only if it has received poor reviews:

```
aws dynamodb delete-item \
    --table-name ProductCatalog \
    --key '{"Id": {"N": "456"}}' \
    --condition-expression "attribute_exists(ProductReviews.OneStar)"
```

**Note**  
For more information about `attribute_not_exists`, `attribute_exists`, and other functions, see [Comparison Operator and Function Reference](Expressions.OperatorsAndFunctions.md)\.

## Conditional Deletes<a name="Expressions.ConditionExpressions.AdvancedComparisons"></a>

To perform a conditional delete, you use a `DeleteItem` operation with a condition expression\. The condition expression must evaluate to true in order for the operation to succeed; otherwise, the operation fails\.

Let us revisit the item from [Condition Expressions](#Expressions.ConditionExpressions):

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

Now suppose that you wanted to delete the item, but only under the following conditions:
+  The `ProductCategory` is either "Sporting Goods" or "Gardening Supplies"
+  The `Price` is between 500 and 600\.

The following example will attempt to delete the item:

```
aws dynamodb delete-item \
    --table-name ProductCatalog \
    --key '{"Id":{"N":"456"}}' \
    --condition-expression "(ProductCategory IN (:cat1, :cat2)) and (Price between :lo and :hi)" \
    --expression-attribute-values file://values.json
```

The arguments for `--expression-attribute-values` are stored in the file `values.json`:

```
{
    ":cat1": {"S": "Sporting Goods"},
    ":cat2": {"S": "Gardening Supplies"},
    ":lo": {"N": "500"},
    ":hi": {"N": "600"}
}
```

**Note**  
In the condition expression, the `:` \(colon character\) indicates an *expression attribute value*— placeholder for an actual value\. For more information, see [Expression Attribute Values](Expressions.ExpressionAttributeValues.md)\.  
For more information about `IN`, `AND`, and other keywords, , see [Comparison Operator and Function Reference](Expressions.OperatorsAndFunctions.md)\.

In this example, the `ProductCategory` comparison evaluates to true, but the `Price` comparison evaluates to false\. This causes the condition expression to evaluate to false, and the DeleteItem operation to fail\.

## Conditional Updates<a name="Expressions.ConditionExpressions.SimpleComparisons"></a>

To perform a conditional update, you use an `UpdateItem` operation with a condition expression\. The condition expression must evaluate to true in order for the operation to succeed; otherwise, the operation fails\.

**Note**  
`UpdateItem` also supports *update expressions*, where you specify the modifications you want to make to an item\. For more information, see [Update Expressions](Expressions.UpdateExpressions.md)\.

Suppose that you started with the item shown in [Condition Expressions](#Expressions.ConditionExpressions):

```
{
    "Id": { "N": "456"},
    "Price": {"N": "650"},
    "ProductCategory": {"S": "Sporting Goods"}
}
```

The following example performs an `UpdateItem` operation\. It attempts to reduce the `Price` of a product by 75—but the condition expression prevents the update if the current `Price` is below 500:

```
aws dynamodb update-item \
    --table-name ProductCatalog \
    --key '{"Id": {"N": "456"}}' \
    --update-expression "SET Price = Price - :discount" \
    --condition-expression "Price > :limit" \
    --expression-attribute-values file://values.json
```

The arguments for `--item` are stored in the file `values.json`:

```
{
    ":discount": { "N": "75"},
    ":limit": {"N": "500"}
}
```

If the starting `Price` is 650, then the `UpdateItem` operation reduces the Price to 575\. If you run the `UpdateItem` operation again, the `Price` is reduced to 500\. If you run it a third time, the condition expression evaluates to false, and the update fails\.

**Note**  
In the condition expression, the `:` \(colon character\) indicates an *expression attribute value*— placeholder for an actual value\. For more information, see [Expression Attribute Values](Expressions.ExpressionAttributeValues.md)\.  
For more information about "*>*" and other operators, see [Comparison Operator and Function Reference](Expressions.OperatorsAndFunctions.md)\.