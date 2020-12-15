# IAM Policy to Grant Permissions to All DynamoDB Actions on a Table<a name="grant-permissions-to-any-action-on-table"></a>

The following policy grants permissions for *all* DynamoDB actions on a table called `Books`\. The resource ARN specified in the `Resource` identifies a table in a specific AWS Region\. If you replace the table name `Books` in the `Resource` ARN with a wildcard character \(\*\), *all* DynamoDB actions are allowed on *all* tables in the account\. Carefully consider the possible security implications before using a wildcard character on this or any IAM policy\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllAPIActionsOnBooks",
            "Effect": "Allow",
            "Action": "dynamodb:*",
            "Resource": "arn:aws:dynamodb:us-west-2:123456789012:table/Books"
        }
    ]
}
```

**Note**  
This is an example of using a wildcard character \(\*\) to allow *all* actions, including administration, data operations, monitoring, and purchase of DynamoDB reserved capacity\. Instead, it is a best practice to explicitly specify each action to be granted and only what that user, role, or group needs\.