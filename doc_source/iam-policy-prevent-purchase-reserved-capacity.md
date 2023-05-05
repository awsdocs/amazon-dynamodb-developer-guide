# IAM policy to prevent the purchase of DynamoDB reserved capacity<a name="iam-policy-prevent-purchase-reserved-capacity"></a>

With Amazon DynamoDB reserved capacity, you pay a one\-time, upfront fee and commit to paying for a minimum usage level at significant savings over a period of time\. You can use the AWS Management Console to view and purchase reserved capacity\. However, you might not want all of the users in your organization to be able to purchase reserved capacity\. For more information about reserved capacity, see [Amazon DynamoDB pricing](https://aws.amazon.com/dynamodb/pricing)\.

DynamoDB provides the following API operations for controlling access to reserved capacity management:
+ `dynamodb:DescribeReservedCapacity` – Returns the reserved capacity purchases that are currently in effect\.
+ `dynamodb:DescribeReservedCapacityOfferings` – Returns details about the reserved capacity plans that are currently offered by AWS\.
+ `dynamodb:PurchaseReservedCapacityOfferings` – Performs an actual purchase of reserved capacity\.

The AWS Management Console uses these API actions to display reserved capacity information and make purchases\. You cannot call these operations from an application program because they can be accessed only from the console\. However, you can allow or deny access to these operations in an IAM permissions policy\.

The following policy allows users to view reserved capacity purchases and offerings by using the AWS Management Console — but new purchases are denied\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowReservedCapacityDescriptions",
            "Effect": "Allow",
            "Action": [
                "dynamodb:DescribeReservedCapacity",
                "dynamodb:DescribeReservedCapacityOfferings"
            ],
            "Resource": "arn:aws:dynamodb:us-west-2:123456789012:*"
        },
        {
            "Sid": "DenyReservedCapacityPurchases",
            "Effect": "Deny",
            "Action": "dynamodb:PurchaseReservedCapacityOfferings",
            "Resource": "arn:aws:dynamodb:us-west-2:123456789012:*"
        }
    ]
}
```

Note that this policy uses the wildcard character \(\*\) to allow describe permissions for all, and to deny the purchase of DynamoDB reserved capacity for all\.