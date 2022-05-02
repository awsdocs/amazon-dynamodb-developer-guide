# IAM Policy to Allow an AWS Lambda Function to Access DynamoDB Stream Records<a name="iam-policy-example-lamda-process-dynamodb-streams"></a>

If you want certain actions to be performed based on events in a DynamoDB stream, you can write an AWS Lambda function that is triggered by these events\. A Lambda function such as this needs permissions to read data from a DynamoDB stream\. For more information about using Lambda with DynamoDB Streams, see [DynamoDB Streams and AWS Lambda Triggers](Streams.Lambda.md)\.

To grant permissions to Lambda, use the permissions policy that is associated with the Lambda function's IAM role \(also known as an execution role\)\. Specify this policy when you create the Lambda function\.

For example, you can associate the following permissions policy with an execution role to grant Lambda permissions to perform the DynamoDB Streams actions listed\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowLambdaFunctionInvocation",
            "Effect": "Allow",
            "Action": [
                "lambda:InvokeFunction"
            ],
            "Resource": [
                "*"
            ]
        },
        {
            "Sid": "APIAccessForDynamoDBStreams",
            "Effect": "Allow",
            "Action": [
                "dynamodb:GetRecords",
                "dynamodb:GetShardIterator",
                "dynamodb:DescribeStream",
                "dynamodb:ListStreams"
            ],
            "Resource": "arn:aws:dynamodb:us-west-2:123456789012:table/GameScores/stream/*"
        }
    ]
}
```

For more information, see [AWS Lambda permissions ](https://docs.aws.amazon.com/lambda/latest/dg/intro-permission-model.html) in the *AWS Lambda Developer Guide*\.