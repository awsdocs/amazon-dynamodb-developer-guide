# IAM Policy to Grant Read Access for a DynamoDB Stream Only \(Not for the Table\)<a name="iam-policy-read-stream-only"></a>

When you enable DynamoDB Streams on a table, information is captured about every modification to items in the table\. For more information, see [Change Data Capture for DynamoDB Streams](Streams.md)\.

In some cases, you might want to prevent an application from reading data from a DynamoDB table, but still allow access to that table's streams\. For example, you can configure AWS Lambda to poll a stream and invoke a Lambda function when item updates are detected, and then perform additional processing\.

The following actions are available for controlling access to DynamoDB streams:
+ `dynamodb:DescribeStream`
+ `dynamodb:GetRecords`
+ `dynamodb:GetShardIterator`
+ `dynamodb:ListStreams`

The following example policy grants users permissions to access the streams of a table named `GameScores`\. The wildcard character \(\*\) in the ARN matches any stream associated with that table\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AccessGameScoresStreamOnly",
            "Effect": "Allow",
            "Action": [
                "dynamodb:DescribeStream",
                "dynamodb:GetRecords",
                "dynamodb:GetShardIterator",
                "dynamodb:ListStreams"
            ],
            "Resource": "arn:aws:dynamodb:us-west-2:123456789012:table/GameScores/stream/*"
        }
    ]
}
```

Note that this policy grants access to the `GameScores` table's streams, but not to the table itself\.