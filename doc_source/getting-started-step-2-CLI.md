# AWS CLI<a name="getting-started-step-2-CLI"></a>

The following AWS CLI example creates two new items in the `Music` table using `put-item`\.

```
aws dynamodb put-item \
--table-name Music  \
--item \
    '{"Artist": {"S": "No One You Know"}, "SongTitle": {"S": "Call Me Today"}, "AlbumTitle": {"S": "Somewhat Famous"}, "Awards": {"N": "1"}}'

aws dynamodb put-item \
    --table-name Music \
    --item \
    '{"Artist": {"S": "Acme Band"}, "SongTitle": {"S": "Happy Day"}, "AlbumTitle": {"S": "Songs About Life"}, "Awards": {"N": "10"} }'
```

For more information about supported data types in DynamoDB, see [Data Types](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.NamingRulesDataTypes.html#HowItWorks.DataTypes)\. 

For more information about how to represent DynamoDB data types in JSON, see [Attribute Values](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_AttributeValue.html)\. 