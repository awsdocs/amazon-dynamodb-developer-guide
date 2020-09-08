# Using Identity\-Based Policies \(IAM Policies\) for Amazon DynamoDB<a name="using-identity-based-policies"></a>

This topic covers identity\-based AWS Identity and Access Management \(IAM\) policies and provides examples\. The examples show how an account administrator can attach permissions policies to IAM identities \(users, groups, and roles\) and thereby grant permissions to perform operations on Amazon DynamoDB resources\.

**Important**  
We recommend that you first review the introductory topics that explain the basic concepts and options available to manage access to your DynamoDB resources\. For more information, see [Overview of Managing Access Permissions to Your Amazon DynamoDB Resources](access-control-overview.md)\. 

The sections in this topic cover the following:
+ [IAM Permissions Required to Use the Amazon DynamoDB Console](#console-permissions)
+ [AWS Managed \(Predefined\) IAM Policies for Amazon DynamoDB](#access-policy-examples-aws-managed)
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

 The policy has one statement that grants permissions for three DynamoDB actions \(`dynamodb:DescribeTable`, `dynamodb:Query`, and `dynamodb:Scan`\) on a table in the `us-west-2` Region, which is owned by the AWS account specified by `account-id`\. The *Amazon Resource Name \(ARN\)* in the `Resource` value specifies the table that the permissions apply to\.

## IAM Permissions Required to Use the Amazon DynamoDB Console<a name="console-permissions"></a>

For a user to work with the DynamoDB console, that user must have a minimum set of permissions that allow the user to work with the DynamoDB resources for their AWS account\. In addition to these DynamoDB permissions, the console requires permissions from the following services:
+ Amazon CloudWatch permissions to display metrics and graphs\.
+ AWS Data Pipeline permissions to export and import DynamoDB data\. 
+  AWS Identity and Access Management permissions to access roles necessary for exports and imports\.
+ Amazon Simple Notification Service permissions to notify you whenever a CloudWatch alarm is triggered\.
+ AWS Lambda permissions to process DynamoDB Streams records\.

If you create an IAM policy that is more restrictive than the minimum required permissions, the console won't function as intended for users with that IAM policy\. To ensure that those users can still use the DynamoDB console, also attach the `AmazonDynamoDBReadOnlyAccess` AWS managed policy to the user, as described in [AWS Managed \(Predefined\) IAM Policies for Amazon DynamoDB](#access-policy-examples-aws-managed)\.

You don't need to allow minimum console permissions for users that are making calls only to the AWS CLI or the Amazon DynamoDB API\.

## AWS Managed \(Predefined\) IAM Policies for Amazon DynamoDB<a name="access-policy-examples-aws-managed"></a>

AWS addresses some common use cases by providing standalone IAM policies that are created and administered by AWS\. These AWS managed policies grant necessary permissions for common use cases so that you can avoid having to investigate what permissions are needed\. For more information, see [AWS Managed Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) in the *IAM User Guide*\.

The following AWS managed policies, which you can attach to users in your account, are specific to DynamoDB and are grouped by use\-case scenario:
+ **AmazonDynamoDBReadOnlyAccess** – Grants read\-only access to DynamoDB resources by using the AWS Management Console\.
+ **AmazonDynamoDBFullAccess** – Grants full access to DynamoDB resources by using the AWS Management Console\.
+ **AmazonDynamoDBFullAccesswithDataPipeline** – Grants full access to DynamoDB resources, including export and import using AWS Data Pipeline, by using the AWS Management Console\.

You can review these AWS managed permissions policies by signing in to the IAM console and searching for specific policies there\.

**Important**  
The best practice is to create custom IAM policies that meet the [least\-privilege](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#grant-least-privilege), access\-control needs of your DynamoDB workloads\. You can attach these custom policies to the IAM users, roles, or groups that require them\. 

## Customer Managed Policy Examples<a name="access-policy-examples-for-sdk-cli"></a>

In this section, you can find example user policies that grant permissions for various DynamoDB actions\. These policies work when you're using AWS SDKs or the AWS CLI\. When you're using the console, you need to grant additional permissions specific to the console, which is discussed in [IAM Permissions Required to Use the Amazon DynamoDB Console](#console-permissions)\.

**Note**  
All examples use the us\-west\-2 Region and contain fictitious account IDs and table names\.

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
Although this is an example of allowing *all* actions with "dynamodb:\*", it's considered a best practice to specify each action to be granted by the policy\. In addition, if you replace the table name in the Resource ARN \(`Books`\) with a wildcard character \(\*\), you allow any DynamoDB actions on *all* tables in the account\. Carefully consider the security implications if you decide to do this\.

### Example 2: Allow Read\-Only Access on Items in a Table<a name="access-policy-examples-for-sdk-cli.example2"></a>

The following permissions policy grants permissions for the `GetItem`, `BatchGetItem`, `Scan`, and `Query` DynamoDB actions only and thereby sets read\-only access to the Books table\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "ReadOnlyAPIActionsOnBooks",
            "Effect": "Allow",
            "Action": [
                "dynamodb:GetItem",
                "dynamodb:BatchGetItem",
                "dynamodb:Scan",
                "dynamodb:Query"
            ],
            "Resource": "arn:aws:dynamodb:us-west-2:123456789012:table/Books"
        }
    ]
}
```

### Example 3: Allow Put, Update, and Delete Operations on a Specific Table<a name="access-policy-examples-for-sdk-cli.example3"></a>

The following permissions policy grants permissions for the `PutItem`, `UpdateItem`, `DeleteItem`, and `BatchWriteItem` actions, thereby setting write\-only access on the Books table\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "WriteOpsOnBooks",
            "Effect": "Allow",
            "Action": [
                "dynamodb:PutItem",
                "dynamodb:UpdateItem",
                "dynamodb:DeleteItem",
                "dynamodb:BatchWriteItem"
            ],
            "Resource": "arn:aws:dynamodb:us-west-2:123456789012:table/Books"
        }
    ]
}
```

### Example 4: Allow Access to a Specific Table and All of Its Indexes<a name="access-policy-examples-for-sdk-cli.example4"></a>

The following permissions policy grants permissions for data modification actions on a DynamoDB table \(`Book`\) and all of that table's indexes\. For more information about how indexes work, see [Improving Data Access with Secondary Indexes](SecondaryIndexes.md)\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AccessAllIndexesOnBooks",
            "Effect": "Allow",
            "Action": [
              "dynamodb:PutItem",
              "dynamodb:UpdateItem",
              "dynamodb:DeleteItem",
              "dynamodb:BatchWriteItem",
              "dynamodb:GetItem",
              "dynamodb:BatchGetItem",
              "dynamodb:Scan",
              "dynamodb:Query"
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

Suppose that you have separate test and production environments where each environment maintains its own version of a table named `ProductCatalog`\. If you create these `ProductCatalog` tables from the same AWS account, testing work might affect the production environment because of the way that permissions are set up\. \(For example, the quotas on concurrent create and delete actions are set at the AWS account level\.\) 

As a result, each action in the test environment reduces the number of actions that are available in your production environment\. There is also a risk that the code in your test environment might accidentally access tables in the production environment\. To prevent these issues, consider creating separate AWS accounts for your test and production environments\.

Suppose further that you have two developers, Amit and Alice, who are testing the `ProductCatalog` table\. Instead of each developer requiring a separate AWS account for every developer, your developers can share the same test account\. In this test account, you can create a copy of the same table for each developer to work on, such as `Alice_ProductCatalog` and `Amit_ProductCatalog`\. In this case, you can create IAM users Alice and Amit in the AWS account that you created for the test environment\. You can then grant permissions to these users to perform DynamoDB actions on the tables that they own\. 

To grant these user permissions, you can do either of the following:
+ Create a separate policy for each user and then attach each policy to its user separately\. For example, you can attach the following policy to user Alice to allow her access to all DynamoDB actions on the `Alice_ProductCatalog` table\. 

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

  Then, you can create a similar policy with a different resource \(the `Amit_ProductCatalog` table\) for user Amit\. 
+ Instead of attaching policies to individual users, you can use IAM policy variables to write a single policy and attach it to a group\. You need to create a group and, for this example, add both users Alice and Amit to the group\. The following example grants permissions to perform all DynamoDB actions on the `${aws:username}_ProductCatalog` table\. The policy variable `${aws:username}` is replaced by the requester's user name when the policy is evaluated\. For example, if Alice sends a request to add an item, the action is allowed only if Alice is adding items to the `Alice_ProductCatalog` table\. 

  ```
  {
      "Version": "2012-10-17",
      "Statement": [
          {
              "Sid": "ActionsOnUserSpecificTable",
              "Effect": "Allow",
              "Action": [
                "dynamodb:PutItem",
                "dynamodb:UpdateItem",
                "dynamodb:DeleteItem",
                "dynamodb:BatchWriteItem",
                "dynamodb:GetItem",
                "dynamodb:BatchGetItem",
                "dynamodb:Scan",
                "dynamodb:Query"
              ],
              "Resource": "arn:aws:dynamodb:us-west-2:123456789012:table/${aws:username}_ProductCatalog"
          },
          {
              "Sid": "AdditionalPrivileges",
              "Effect": "Allow",
              "Action": [
                  "dynamodb:ListTables",
                  "dynamodb:DescribeTable",
                  "dynamodb:DescribeContributorInsights"
              ],
              "Resource": "arn:aws:dynamodb:us-west-2:123456789012:table/*"
          }
      ]
  }
  ```

**Note**  
When using IAM policy variables, you must explicitly specify the `2012-10-17` version of the access policy language in the policy\. The default version of the access policy language \(`2008-10-17`\) does not support policy variables\. 

Instead of identifying a specific table as a resource, like you normally would, you could intentionally use a wildcard character \(\*\) to grant permissions on all tables where the table name is prefixed with the IAM user that is making the request, as shown in the following example\.

```
"Resource":"arn:aws:dynamodb:us-west-2:123456789012:table/${aws:username}_*"
```

### Example 6: Prevent a User from Purchasing Reserved Capacity Offerings<a name="access-policy-examples-for-sdk-cli.example6"></a>

DynamoDB customers can purchase reserved capacity, as described at [Amazon DynamoDB pricing](https://aws.amazon.com/dynamodb/pricing)\. With reserved capacity, you pay a one\-time upfront fee and commit to paying for a minimum usage level, at significant savings, over a period of time\. You can use the AWS Management Console to view and purchase reserved capacity\. However, you might not want all of the users in your organization to be able to purchase reserve capacity\.

DynamoDB provides the following API operations for controlling access to reserved capacity management:
+ `dynamodb:DescribeReservedCapacity` – Returns the reserved capacity purchases that are currently in effect\.
+ `dynamodb:DescribeReservedCapacityOfferings` – Returns details about the reserved capacity plans that are currently offered by AWS\.
+ `dynamodb:PurchaseReservedCapacityOfferings` – Performs an actual purchase of reserved capacity\.

The AWS Management Console uses these API operations to display reserved capacity information and to make purchases\. You cannot call these operations from an application program because they can be accessed only from the console\. However, you can allow or deny access to these operations in an IAM permissions policy\.

The following policy allows users to view reserved capacity purchases and offerings by using the AWS Management Console—but new purchases are denied\.

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

Note that this policy uses the wildcard character \(\*\) to allow describe permissions for all and deny purchase of DynamoDB reserved capacity for all\.

### Example 7: Allow Read Access for a DynamoDB Stream Only \(Not for the Table\)<a name="access-policy-examples-for-sdk-cli.example7"></a>

When you enable DynamoDB Streams on a table, information is captured about every modification to data items in the table\. For more information, see [Capturing Table Activity with DynamoDB Streams](Streams.md)\.

In some cases, you might want to prevent an application from reading data from a DynamoDB table, but still allow access to that table's streams\. For example, you can configure AWS Lambda to poll a stream and invoke a Lambda function when item updates are detected, and then perform additional processing\.

The following actions are available for controlling access to DynamoDB Streams:
+ `dynamodb:DescribeStream`
+ `dynamodb:GetRecords`
+ `dynamodb:GetShardIterator`
+ `dynamodb:ListStreams`

The following example creates a policy that grants users permissions to access the streams of a table named `GameScores`\. The final wildcard character \(\*\) in the ARN matches any stream ID associated with that table\.

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

Note that this policy allows access to the `GameScores` table's streams, but not to the table itself\.

### Example 8: Allow an AWS Lambda Function to Process DynamoDB Stream Records<a name="access-policy-examples-for-sdk-cli.example8"></a>

If you want certain actions to be performed based on new events in a DynamoDB stream, you can write an AWS Lambda function that is triggered by these new events\. For more information about using Lambda with stream events, see [DynamoDB Streams and AWS Lambda Triggers](Streams.Lambda.md)\. A Lambda function such as this needs permissions to read data from the DynamoDB stream\.

To grant permissions to Lambda, you use the permissions policy that is associated with the Lambda function's IAM role \(execution role\)\.  You specify this policy when you create the Lambda function\.

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
            "Sid": "APIAccessForDynamoDBStreams",
            "Effect": "Allow",
            "Action": [
                "dynamodb:GetRecords",
                "dynamodb:GetShardIterator",
                "dynamodb:DescribeStream",
                "dynamodb:ListStreams"
            ],
            "Resource": "arn:aws:dynamodb:us-west-2:123456789012:table/GameScores/streams/*"
        }
    ]
}
```

 In the *IAM User Guide*, there are three additional DynamoDB [example policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_examples.html): 
+ [Amazon DynamoDB: Allows Access to a Specific Table](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_examples_dynamodb_specific-table.html)
+ [Amazon DynamoDB: Allows Access to Specific Columns](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_examples_dynamodb_columns.html)
+ [Amazon DynamoDB: Allows Row\-Level Access to DynamoDB Based on an Amazon Cognito ID](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_examples_dynamodb_rows.html)

For more information, see [AWS Lambda permissions model](https://docs.aws.amazon.com/lambda/latest/dg/intro-permission-model.html) in the *AWS Lambda Developer Guide*\.