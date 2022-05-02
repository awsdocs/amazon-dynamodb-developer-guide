# IAM Policy to Separate DynamoDB Environments in the Same AWS Account<a name="iam-policy-separate-environments"></a>

Suppose that you have separate environments where each environment maintains its own version of a table named `ProductCatalog`\. If you create two `ProductCatalog` tables in the same AWS account, work in one environment might affect the other environment because of the way that permissions are set up\. For example, quotas on the number of concurrent control plane operations \(such as `CreateTable`\) are set at the AWS account level\.

As a result, each action in one environment reduces the number of operations available in the other environment\. There is also a risk that the code in one environment might accidentally access tables in the other environment\.

**Note**  
If you want to separate production and test workloads to help control an event's potential "blast radius," the best practice is to create separate AWS accounts for test and production workloads\. For more information, see [AWS Account Management and Separation](https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/aws-account-management-and-separation.html)\.

Suppose further that you have two developers, Amit and Alice, who are testing the `ProductCatalog` table\. Instead of each developer requiring a separate AWS account, your developers can share the same test AWS account\. In this test account, you can create a copy of the same table for each developer to work on, such as `Alice_ProductCatalog` and `Amit_ProductCatalog`\. In this case, you can create IAM users Alice and Amit in the AWS account that you created for the test environment\. You then can grant permissions to these users to perform DynamoDB actions on the tables that they own\. 

To grant these IAM user permissions, you can do either of the following:
+ Create a separate policy for each user, and then attach each policy to its user separately\. For example, you can attach the following policy to user Alice to allow her access to DynamoDB actions on the `Alice_ProductCatalog` table: 

  ```
  {
      "Version": "2012-10-17",
      "Statement": [
          {
              "Sid": "AllAPIActionsOnAliceTable",
              "Effect": "Allow",
              "Action": [
                "dynamodb:DeleteItem",
                "dynamodb:DescribeContributorInsights",
                "dynamodb:RestoreTableToPointInTime",
                "dynamodb:ListTagsOfResource",
                "dynamodb:CreateTableReplica",
                "dynamodb:UpdateContributorInsights",
                "dynamodb:CreateBackup",
                "dynamodb:DeleteTable",
                "dynamodb:UpdateTableReplicaAutoScaling",
                "dynamodb:UpdateContinuousBackups",
                "dynamodb:TagResource",
                "dynamodb:DescribeTable",
                "dynamodb:GetItem",
                "dynamodb:DescribeContinuousBackups",
                "dynamodb:BatchGetItem",
                "dynamodb:UpdateTimeToLive",
                "dynamodb:BatchWriteItem",
                "dynamodb:ConditionCheckItem",
                "dynamodb:UntagResource",
                "dynamodb:PutItem",
                "dynamodb:Scan",
                "dynamodb:Query",
                "dynamodb:UpdateItem",
                "dynamodb:DeleteTableReplica",
                "dynamodb:DescribeTimeToLive",
                "dynamodb:RestoreTableFromBackup",
                "dynamodb:UpdateTable",
                "dynamodb:DescribeTableReplicaAutoScaling",
                "dynamodb:GetShardIterator",
                "dynamodb:DescribeStream",
                "dynamodb:GetRecords",
                "dynamodb:DescribeLimits",
                "dynamodb:ListStreams"
              ],
              "Resource": "arn:aws:dynamodb:us-west-2:123456789012:table/Alice_ProductCatalog/*"
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
                "dynamodb:Query",
                "dynamodb:ConditionCheckItem"
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
When using IAM policy variables, you must explicitly specify the `2012-10-17` version of the IAM policy language in the policy\. The default version of the IAM policy language \(`2008-10-17`\) does not support policy variables\. 

Instead of identifying a specific table as a resource as you normally would, you could use a wildcard character \(\*\) to grant permissions on all tables where the table name is prefixed with the IAM user that is making the request, as shown in the following example\.

```
"Resource":"arn:aws:dynamodb:us-west-2:123456789012:table/${aws:username}_*"
```