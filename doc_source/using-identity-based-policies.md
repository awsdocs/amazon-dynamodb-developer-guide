# Using Identity\-Based Policies \(IAM Policies\) for Amazon DynamoDB<a name="using-identity-based-policies"></a>

This topic provides examples of identity\-based policies that demonstrate how an account administrator can attach permissions policies to IAM identities \(that is, users, groups, and roles\) and thereby grant permissions to perform operations on Amazon DynamoDB resources\.

**Important**  
We recommend that you first review the introductory topics that explain the basic concepts and options available to manage access to your Amazon DynamoDB resources\. For more information, see [Overview of Managing Access Permissions to Your Amazon DynamoDB Resources](access-control-overview.md)\. 

The sections in this topic cover the following:
+ [Permissions Required to Use the Amazon DynamoDB Console](#console-permissions)
+ [AWS Managed \(Predefined\) Policies for Amazon DynamoDB](#access-policy-examples-aws-managed)
+ [Customer Managed Policy Examples](#access-policy-examples-for-sdk-cli)

The following shows an example of a permissions policy\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "DescribeQueryScanBooksTable",
            "Effect": "Allow",
            "Action": [
                "dynamodb:DescribeTable",
                "dynamodb:Query",
                "dynamodb:Scan"
            ],
            "Resource": "arn:aws:dynamodb:us-west-2:account-id:table/Books"
        }
    ]
}
```

 The policy has one statement that grants permissions for three DynamoDB actions \(`dynamodb:DescribeTable`, `dynamodb:Query`, and `dynamodb:Scan`\) on a table in the `us-west-2` Region, which is owned by the AWS account specified by `account-id`\. The *Amazon Resource Name \(ARN\)* in the `Resource` value specifies the table to which the permissions apply\.

## Permissions Required to Use the Amazon DynamoDB Console<a name="console-permissions"></a>

For a user to work with the DynamoDB console, that user must have a minimum set of permissions that allow the user to work with the DynamoDB resources for their AWS account\. In addition to these DynamoDB permissions, the console requires permissions from the following services:
+ Amazon CloudWatch permissions to display metrics and graphs\.
+ AWS Data Pipeline permissions to export and import DynamoDB data\. 
+  AWS Identity and Access Management permissions to access roles necessary for exports and imports\.
+ Amazon Simple Notification Service permissions to notify you whenever a CloudWatch alarm is triggered\.
+ AWS Lambda permissions to process DynamoDB Streams records\.

If you create an IAM policy that is more restrictive than the minimum required permissions, the console won't function as intended for users with that IAM policy\. To ensure that those users can still use the DynamoDB console, also attach the `AmazonDynamoDBReadOnlyAccess` managed policy to the user, as described in [AWS Managed \(Predefined\) Policies for Amazon DynamoDB](#access-policy-examples-aws-managed)\.

You don't need to allow minimum console permissions for users that are making calls only to the AWS CLI or the Amazon DynamoDB API\.

## AWS Managed \(Predefined\) Policies for Amazon DynamoDB<a name="access-policy-examples-aws-managed"></a>

AWS addresses many common use cases by providing standalone IAM policies that are created and administered by AWS\. These AWS managed policies grant necessary permissions for common use cases so that you can avoid having to investigate what permissions are needed\. For more information, see [AWS Managed Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) in the *IAM User Guide*\. 

The following AWS managed policies, which you can attach to users in your account, are specific to DynamoDB and are grouped by use case scenario:
+ **AmazonDynamoDBReadOnlyAccess** – Grants read\-only access to DynamoDB resources by using the AWS Management Console\.
+ **AmazonDynamoDBFullAccess** – Grants full access to DynamoDB resources by using the AWS Management Console\.
+ **AmazonDynamoDBFullAccesswithDataPipeline** – Grants full access to DynamoDB resources, including export and import using AWS Data Pipeline, by using AWS Management Console\.

**Note**  
You can review these permissions policies by signing in to the IAM console and searching for specific policies there\.

We do not recommend using these AWS managed policies for production databases\. Instead, you should create your own custom IAM policies to allow permissions for DynamoDB actions and resources following the [least privilege model](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#grant-least-privilege)\. You can attach these custom policies to the IAM users or groups that require those permissions\.

## Customer Managed Policy Examples<a name="access-policy-examples-for-sdk-cli"></a>

In this section, you can find example user policies that grant permissions for various DynamoDB actions\. These policies work when you are using AWS SDKs or the AWS CLI\. When you are using the console, you need to grant additional permissions specific to the console, which is discussed in [Permissions Required to Use the Amazon DynamoDB Console](#console-permissions)\.

**Note**  
All examples use the us\-west\-2 Region and contain fictitious account IDs\.

**Topics**
+ [Example 1: Allow a User to Perform Any DynamoDB Actions on a Table](#access-policy-examples-for-sdk-cli.example1)
+ [Example 2: Allow Read\-Only Access on Items in a Table](#access-policy-examples-for-sdk-cli.example2)
+ [Example 3: Allow Put, Update, and Delete Operations on a Specific Table](#access-policy-examples-for-sdk-cli.example3)
+ [Example 4: Allow Access to a Specific Table and All of Its Indexes](#access-policy-examples-for-sdk-cli.example4)
+ [Example 5: Set Up Permissions Policies for Separate Test and Production Environments](#access-policy-examples-for-sdk-cli.example5)
+ [Example 6: Prevent a User from Purchasing Reserved Capacity Offerings](#access-policy-examples-for-sdk-cli.example6)
+ [Example 7: Allow Read Access for a DynamoDB Stream Only \(Not for the Table\)](#access-policy-examples-for-sdk-cli.example7)
+ [Example 8: Allow an AWS Lambda Function to Process DynamoDB Stream Records](#access-policy-examples-for-sdk-cli.example8)

### Example 1: Allow a User to Perform Any DynamoDB Actions on a Table<a name="access-policy-examples-for-sdk-cli.example1"></a>

The following permissions policy grants permissions for all DynamoDB actions on a table\. The ARN value specified in the `Resource` identifies a table in a specific Region\.

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
If you replace the table name in the resource ARN \(`Books`\) with a wildcard character \(\*\), you allow any DynamoDB actions on *all* tables in the account\. Carefully consider the security implications if you decide to do this\.

### Example 2: Allow Read\-Only Access on Items in a Table<a name="access-policy-examples-for-sdk-cli.example2"></a>

The following permissions policy grants permissions for the `GetItem` and `BatchGetItem` DynamoDB actions only and thereby sets read\-only access to a table\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "ReadOnlyAPIActionsOnBooks",
            "Effect": "Allow",
            "Action": [
                "dynamodb:GetItem",
                "dynamodb:BatchGetItem"
            ],
            "Resource": "arn:aws:dynamodb:us-west-2:123456789012:table/Books"
        }
    ]
}
```

### Example 3: Allow Put, Update, and Delete Operations on a Specific Table<a name="access-policy-examples-for-sdk-cli.example3"></a>

The following permissions policy grants permissions for the `PutItem`, `UpdateItem`, and `DeleteItem` actions on a specific DynamoDB table\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PutUpdateDeleteOnBooks",
            "Effect": "Allow",
            "Action": [
                "dynamodb:PutItem",
                "dynamodb:UpdateItem",
                "dynamodb:DeleteItem"
            ],
            "Resource": "arn:aws:dynamodb:us-west-2:123456789012:table/Books"
        }
    ]
}
```

### Example 4: Allow Access to a Specific Table and All of Its Indexes<a name="access-policy-examples-for-sdk-cli.example4"></a>

The following permissions policy grants permissions for all of the DynamoDB actions on a table \(`Book`\) and all of the table's indexes\. For more information about how indexes work, see [Improving Data Access with Secondary Indexes](SecondaryIndexes.md)\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AccessAllIndexesOnBooks",
            "Effect": "Allow",
            "Action": [
                "dynamodb:*"
            ],
            "Resource": [
                "arn:aws:dynamodb:us-west-2:123456789012:table/Books",
                "arn:aws:dynamodb:us-west-2:123456789012:table/Books/index/*"
            ]
        }
    ]
}
```

### Example 5: Set Up Permissions Policies for Separate Test and Production Environments<a name="access-policy-examples-for-sdk-cli.example5"></a>

Suppose that you have separate test and production environments where each environment maintains its own version of a table named `ProductCatalog`\. If you create these `ProductCatalog` tables from the same AWS account, testing work might affect the production environment because of the way that permissions are set up\. \(For example, the limits on concurrent create and delete actions are set at the AWS account level\.\) As a result, each action in the test environment reduces the number of actions that are available in your production environment\. There is also a risk that the code in your test environment might accidentally access tables in the production environment\. To prevent these issues, consider creating separate AWS accounts for your production and test environments\.

Suppose further that you have two developers, Bob and Alice, who are testing the `ProductCatalog` table\. Instead of creating a separate AWS account for every developer, your developers can share the same test account\. In this test account, you can create a copy of the same table for each developer to work on, such as `Alice_ProductCatalog` and `Bob_ProductCatalog`\. In this case, you can create IAM users Alice and Bob in the AWS account that you created for the test environment\. You can then grant permissions to these users to perform DynamoDB actions on the tables that they own\. 

To grant these user permissions, you can do either of the following:
+ Create a separate policy for each user and then attach each policy to its user separately\. For example, you can attach the following policy to user Alice to allow her access to all DynamoDB actions on the `Alice_ProductCatalog` table: 

  ```
  {
      "Version": "2012-10-17",
      "Statement": [
          {
              "Sid": "AllAPIActionsOnAliceTable",
              "Effect": "Allow",
              "Action": [
                  "dynamodb:*"
              ],
              "Resource": "arn:aws:dynamodb:us-west-2:123456789012:table/Alice_ProductCatalog"
          }
      ]
  }
  ```

  Then, you can create a similar policy with a different resource \(`Bob_ProductCatalog` table\) for user Bob\. 
+ Instead of attaching policies to individual users, you can use IAM policy variables to write a single policy and attach it to a group\. You need to create a group and, for this example, add both users Alice and user Bob to the group\. The following example grants permissions to perform all DynamoDB actions on the `${aws:username}_ProductCatalog` table\. The policy variable `${aws:username}` is replaced by the requester's user name when the policy is evaluated\. For example, if Alice sends a request to add an item, the action is allowed only if Alice is adding items to the `Alice_ProductCatalog` table\. 

  ```
  {
      "Version": "2012-10-17",
      "Statement": [
          {
              "Sid": "AllAPIActionsOnUserSpecificTable",
              "Effect": "Allow",
              "Action": [
                  "dynamodb:*"
              ],
              "Resource": "arn:aws:dynamodb:us-west-2:123456789012:table/${aws:username}_ProductCatalog"
          },
          {
              "Sid": "AdditionalPrivileges",
              "Effect": "Allow",
              "Action": [
                  "dynamodb:ListTables",
                  "dynamodb:DescribeTable",
                  "cloudwatch:*",
                  "sns:*"
              ],
              "Resource": "*"
          }
      ]
  }
  ```

**Note**  
When using IAM policy variables, you must explicitly specify the `2012-10-17` version of the access policy language in the policy\. The default version of the access policy language \(`2008-10-17`\) does not support policy variables\. 

Instead of identifying a specific table as a resource, you can use a wildcard character \(\*\) to grant permissions on all tables where the name is prefixed with the name of the IAM user that is making the request, as shown following\.

```
"Resource":"arn:aws:dynamodb:us-west-2:123456789012:table/${aws:username}_*"
```

### Example 6: Prevent a User from Purchasing Reserved Capacity Offerings<a name="access-policy-examples-for-sdk-cli.example6"></a>

DynamoDB customers can purchase reserved capacity, as described at [Amazon DynamoDB Pricing](https://aws.amazon.com/dynamodb/pricing)\. With reserved capacity, you pay a one\-time upfront fee and commit to paying for a minimum usage level, at significant savings, over a period of time\. You can use the AWS Management Console to view and purchase reserved capacity\. However, you might not want all of the users in your organization to have the same levels of access\.

DynamoDB provides the following API operations for controlling access to reserved capacity management:
+ `dynamodb:DescribeReservedCapacity` – returns the reserved capacity purchases that are currently in effect\.
+ `dynamodb:DescribeReservedCapacityOfferings` – returns details about the reserved capacity plans that are currently offered by AWS\.
+ `dynamodb:PurchaseReservedCapacityOfferings` – performs an actual purchase of reserved capacity\.

The AWS Management Console uses these API operations to display reserved capacity information and to make purchases\. You cannot call these operations from an application program, because they can be accessed only from the console\. However, you can allow or deny access to these operations in an IAM permissions policy\.

The following policy allows users to view reserved capacity offerings and current purchases using the AWS Management Console—but new purchases are denied\.

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

### Example 7: Allow Read Access for a DynamoDB Stream Only \(Not for the Table\)<a name="access-policy-examples-for-sdk-cli.example7"></a>

When you enable DynamoDB Streams on a table, it captures information about every modification to data items in the table\. For more information, see [Capturing Table Activity with DynamoDB Streams](Streams.md)\.

In some cases, you might want to prevent an application from reading data from a DynamoDB table, while still allowing access to that table's stream\. For example, you can configure AWS Lambda to poll the stream and invoke a Lambda function when item updates are detected, and then perform additional processing\.

The following actions are available for controlling access to DynamoDB Streams:
+ `dynamodb:DescribeStream`
+ `dynamodb:GetRecords`
+ `dynamodb:GetShardIterator`
+ `dynamodb:ListStreams`

The following example creates a policy that grants users permissions to access the streams on a table named `GameScores`\. The final wildcard character \(\*\) in the ARN matches any stream ID associated with that table\.

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

Note that this policy permits access to the streams on the `GameScores` table, but not to the table itself\.

### Example 8: Allow an AWS Lambda Function to Process DynamoDB Stream Records<a name="access-policy-examples-for-sdk-cli.example8"></a>

If you want certain actions to be performed based on new events in a DynamoDB stream, you can write an AWS Lambda function that is triggered by these new events\. For more information about using Lambda with stream events, see [DynamoDB Streams and AWS Lambda Triggers](Streams.Lambda.md)\. A Lambda function such as this needs permissions to read data from the DynamoDB stream\.

To grant permissions to Lambda, you use the permissions policy that is associated with the Lambda function's IAM role \(execution role\), which you specify when you create the Lambda function\.

For example, you can associate the following permissions policy with the execution role to grant Lambda permissions to perform the DynamoDB Streams actions listed\.

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
            "Sid": "AllAPIAccessForDynamoDBStreams",
            "Effect": "Allow",
            "Action": [
                "dynamodb:GetRecords",
                "dynamodb:GetShardIterator",
                "dynamodb:DescribeStream",
                "dynamodb:ListStreams"
            ],
            "Resource": "*"
        }   
    ] 
}
```

For more information, see [AWS Lambda Permission Model](https://docs.aws.amazon.com/lambda/latest/dg/intro-permission-model.html) in the *AWS Lambda Developer Guide*\.