# AttributesToGet<a name="LegacyConditionalParameters.AttributesToGet"></a>

**Note**  
With the introduction of expression parameters, several older parameters have been deprecated\. New applications should not use these legacy parameters, but should use expression parameters instead\. For more information, see [Using expressions in DynamoDB](Expressions.md)\.

`AttributesToGet` is an array of one or more attributes to retrieve from DynamoDB\. If no attribute names are provided, then all attributes will be returned\. If any of the requested attributes are not found, they will not appear in the result\.

`AttributesToGet` allows you to retrieve attributes of type List or Map; however, it cannot retrieve individual elements within a List or a Map\.

Note that `AttributesToGet` has no effect on provisioned throughput consumption\. DynamoDB determines capacity units consumed based on item size, not on the amount of data that is returned to an application\.

## Use *ProjectionExpression* instead<a name="w120aac41c23c15c11"></a>

Suppose you wanted to retrieve an item from the *Music* table, but that you only wanted to return some of the attributes\. You could use a `GetItem` request with an `AttributesToGet` parameter, as in this AWS CLI example:

```
aws dynamodb get-item \
    --table-name Music \
    --attributes-to-get '["Artist", "Genre"]' \
    --key '{
        "Artist": {"S":"No One You Know"},
        "SongTitle": {"S":"Call Me Today"}
    }'
```

But you could use a `ProjectionExpression` instead:

```
aws dynamodb get-item \
    --table-name Music \
    --projection-expression "Artist, Genre" \
    --key '{
        "Artist": {"S":"No One You Know"},
        "SongTitle": {"S":"Call Me Today"}
    }'
```