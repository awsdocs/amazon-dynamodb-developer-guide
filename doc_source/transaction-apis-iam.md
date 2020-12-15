# Using IAM with DynamoDB Transactions<a name="transaction-apis-iam"></a>

You can use AWS Identity and Access Management \(IAM\) to restrict the actions that transactional operations can perform in Amazon DynamoDB\. For more information about using IAM policies in DynamoDB, see [Using Identity\-Based Policies \(IAM Policies\) with Amazon DynamoDB](using-identity-based-policies.md)\.

Permissions for `Put`, `Update`, `Delete`, and `Get` actions are governed by the permissions used for the underlying `PutItem`, `UpdateItem`, `DeleteItem`, and `GetItem` operations\. For the `ConditionCheck` action, you can use the `dynamodb:ConditionCheck` permission in IAM policies\.

The following are examples of IAM policies that you can use to configure the DynamoDB transactions\.

## Example 1: Allow Transactional Operations<a name="tx-policy-example-1"></a>

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "dynamodb:ConditionCheckItem",
                "dynamodb:PutItem",
                "dynamodb:UpdateItem",
                "dynamodb:DeleteItem",
                "dynamodb:GetItem"
            ],
            "Resource": [
                "arn:aws:dynamodb:*:*:table/table04"
            ]
        }
    ]
}
```

## Example 2: Allow Only Transactional Operations<a name="tx-policy-example-2"></a>

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "dynamodb:ConditionCheckItem",
                "dynamodb:PutItem",
                "dynamodb:UpdateItem",
                "dynamodb:DeleteItem",
                "dynamodb:GetItem"
            ],
            "Resource": [
                "arn:aws:dynamodb:*:*:table/table04"
            ],
            "Condition": {
                "ForAnyValue:StringEquals": {
                    "dynamodb:EnclosingOperation": [
                        "TransactWriteItems",
                        "TransactGetItems"
                    ]
                }
            }
        }
    ]
}
```

## Example 3: Allow Nontransactional Reads and Writes, and Block Transactional Reads and Writes<a name="tx-policy-example-3"></a>

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Deny",
            "Action": [
                "dynamodb:ConditionCheckItem",
                "dynamodb:PutItem",
                "dynamodb:UpdateItem",
                "dynamodb:DeleteItem",
                "dynamodb:GetItem"
            ],
            "Resource": [
                "arn:aws:dynamodb:*:*:table/table04"
            ],
            "Condition": {
                "ForAnyValue:StringEquals": {
                    "dynamodb:EnclosingOperation": [
                        "TransactWriteItems",
                        "TransactGetItems"
                    ]
                }
            }
        },
        {
            "Effect": "Allow",
             "Action": [
                 "dynamodb:PutItem",
                 "dynamodb:DeleteItem",
                 "dynamodb:GetItem",
                 "dynamodb:UpdateItem"
             ],
             "Resource": [
                 "arn:aws:dynamodb:*:*:table/table04"
             ]
         }
    ]
}
```

## Example 4: Prevent Information from Being Returned on a ConditionCheck Failure<a name="tx-policy-example-4"></a>

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "dynamodb:ConditionCheckItem",
                "dynamodb:PutItem",
                "dynamodb:UpdateItem",
                "dynamodb:DeleteItem",
                "dynamodb:GetItem"
            ],
            "Resource": "arn:aws:dynamodb:*:*:table/table01",
            "Condition": {
                "StringEqualsIfExists": {
                    "dynamodb:ReturnValues": "NONE"
                }
            }
        }
    ]
}
```