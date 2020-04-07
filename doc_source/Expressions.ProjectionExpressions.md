# Projection Expressions<a name="Expressions.ProjectionExpressions"></a>

To read data from a table, you use operations such as `GetItem`, `Query`, or `Scan`\. Amazon DynamoDB returns all the item attributes by default\. To get only some, rather than all of the attributes, use a projection expression\.

A *projection expression* is a string that identifies the attributes that you want\. To retrieve a single attribute, specify its name\. For multiple attributes, the names must be comma\-separated\.

The following are some examples of projection expressions, based on the `ProductCatalog` item from [Specifying Item Attributes When Using Expressions](Expressions.Attributes.md):
+ A single top\-level attribute\.

  `Title `
+ Three top\-level attributes\. DynamoDB retrieves the entire `Color` set\.

  `Title, Price, Color`
+ Four top\-level attributes\. DynamoDB returns the entire contents of `RelatedItems` and `ProductReviews`\.

  `Title, Description, RelatedItems, ProductReviews`

You can use any attribute name in a projection expression, provided that the first character is `a-z` or `A-Z` and the second character \(if present\) is `a-z`, `A-Z`, or `0-9`\. If an attribute name does not meet this requirement, you must define an expression attribute name as a placeholder\. For more information, see [Expression Attribute Names in DynamoDB](Expressions.ExpressionAttributeNames.md)\.

The following AWS CLI example shows how to use a projection expression with a `GetItem` operation\. This projection expression retrieves a top\-level scalar attribute \(`Description`\), the first element in a list \(`RelatedItems[0]`\), and a list nested within a map \(`ProductReviews.FiveStar`\)\.

```
aws dynamodb get-item \
    --table-name ProductCatalog \
    --key file://key.json \
    --projection-expression "Description, RelatedItems[0], ProductReviews.FiveStar"
```

The arguments for `--key` are stored in the `key.json` file\.

```
{
    "Id": { "N": "123" }
}
```

For programming language\-specific code examples, see [Getting Started with DynamoDB and AWS SDKs](GettingStarted.md)\.