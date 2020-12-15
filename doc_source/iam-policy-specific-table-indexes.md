# IAM Policy to Grant Access to a Specific DynamoDB Table and Its Indexes<a name="iam-policy-specific-table-indexes"></a>

The following policy grants permissions for data modification actions on a DynamoDB table called `Books` and all of that table's indexes\. For more information about how indexes work, see [Improving Data Access with Secondary Indexes](SecondaryIndexes.md)\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AccessTableAllIndexesOnBooks",
            "Effect": "Allow",
            "Action": [
              "dynamodb:PutItem",
              "dynamodb:UpdateItem",
              "dynamodb:DeleteItem",
              "dynamodb:BatchWriteItem",
              "dynamodb:GetItem",
              "dynamodb:BatchGetItem",
              "dynamodb:Scan",
              "dynamodb:Query",
              "dynamodb:ConditionCheckItem"
            ],
            "Resource": [
                "arn:aws:dynamodb:us-west-2:123456789012:table/Books",
                "arn:aws:dynamodb:us-west-2:123456789012:table/Books/index/*"
            ]
        }
    ]
}
```