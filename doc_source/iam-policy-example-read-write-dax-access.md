# IAM Policy for Read and Write Access to a DynamoDB Accelerator \(DAX\) Cluster<a name="iam-policy-example-read-write-dax-access"></a>

The following policy allows read, write, update, and delete access to a DynamoDB Accelerator \(DAX\) cluster, but not to the associated DynamoDB table\. To use this policy, substitute the AWS Region name, your account ID, and the name of your DAX cluster\.

**Note**  
This policy gives access to DAX cluster, but not to the associated DynamoDB table\. Make sure that your DAX cluster has the correct policy to perform these same operations on the DynamoDB table on your behalf\. 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AmazonDynamoDBDAXDataOperations",
            "Effect": "Allow",
            "Action": [
                "dax:GetItem",
                "dax:PutItem",
                "dax:ConditionCheckItem",
                "dax:BatchGetItem",
                "dax:BatchWriteItem",
                "dax:DeleteItem",
                "dax:Query",
                "dax:UpdateItem",
                "dax:Scan"
            ],
            "Resource": "arn:aws:dax:eu-west-1:123456789012:cache/MyDAXCluster"
        }
    ]
}
```

To expand this policy to cover DAX access for all AWS Regions for an account, use a wildcard character \(\*\) for the Region name\.

```
"Resource": "arn:aws:dax:*:123456789012:cache/MyDAXCluster"
```