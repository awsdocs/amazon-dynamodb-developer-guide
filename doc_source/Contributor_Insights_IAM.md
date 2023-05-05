# Using IAM with CloudWatch contributor insights for DynamoDB<a name="Contributor_Insights_IAM"></a>

## <a name="Contributor_Insights_IAM.permissions-model"></a>

The first time that you enable Amazon CloudWatch Contributor Insights for Amazon DynamoDB, DynamoDB automatically creates an AWS Identity and Access Management \(IAM\) service\-linked role for you\. This role, `AWSServiceRoleForDynamoDBCloudWatchContributorInsights`, allows DynamoDB to manage CloudWatch Contributor Insights rules on your behalf\. Don't delete this service\-linked role\. If you delete it, all your managed rules will no longer be cleaned up when you delete your table or global secondary index\.

For more information about service\-linked roles, see [Using service\-linked roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html) in the *IAM User Guide*\.

The following permissions are required:
+ To enable or disable CloudWatch Contributor Insights for DynamoDB, you must have `dynamodb:UpdateContributorInsights` permission on the table or index\.
+ To view CloudWatch Contributor Insights for DynamoDB graphs, you must have `cloudwatch:GetInsightRuleReport` permission\.
+ To describe CloudWatch Contributor Insights for DynamoDB for a given DynamoDB table or index, you must have `dynamodb:DescribeContributorInsights` permission\.
+ To list CloudWatch Contributor Insights for DynamoDB statuses for each table and global secondary index, you must have `dynamodb:ListContributorInsights` permission\.

## Example: Enable or disable CloudWatch contributor insights for DynamoDB<a name="access-policy-Contributor_Insights-example1"></a>

The following IAM policy grants permissions to enable or disable CloudWatch Contributor Insights for DynamoDB\. 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "iam:CreateServiceLinkedRole",
            "Resource": "arn:aws:iam::*:role/aws-service-role/contributorinsights.dynamodb.amazonaws.com/AWSServiceRoleForDynamoDBCloudWatchContributorInsights",
            "Condition": {"StringLike": {"iam:AWSServiceName": "contributorinsights.dynamodb.amazonaws.com"}}
        },
        {
            "Effect": "Allow",
            "Action": [
                "dynamodb:UpdateContributorInsights"
            ],
            "Resource": "arn:aws:dynamodb:*:*:table/*"
        }
    ]
}
```

 For tables encryped by KMS key, the user needs to have kms:Decrypt permissions in order to update Contributor Insights\. 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "iam:CreateServiceLinkedRole",
            "Resource": "arn:aws:iam::*:role/aws-service-role/contributorinsights.dynamodb.amazonaws.com/AWSServiceRoleForDynamoDBCloudWatchContributorInsights",
            "Condition": {"StringLike": {"iam:AWSServiceName": "contributorinsights.dynamodb.amazonaws.com"}}
        },
        {
            "Effect": "Allow",
            "Action": [
                "dynamodb:UpdateContributorInsights"
            ],
            "Resource": "arn:aws:dynamodb:*:*:table/*"
        },
        {
            "Effect": "Allow",
            "Resource": "arn:aws:kms:*:*:key/*",
            "Action": [
                "kms:Decrypt"
            ],
        }
    ]
}
```

## Example: Retrieve CloudWatch contributor insights rule report<a name="access-policy-Contributor_Insights-example2"></a>

The following IAM policy grants permissions to retrieve CloudWatch Contributor Insights rule report\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "cloudwatch:GetInsightRuleReport"
            ],
            "Resource": "arn:aws:cloudwatch:*:*:insight-rule/DynamoDBContributorInsights*"
        }
    ]
}
```

## Example: Selectively apply CloudWatch contributor insights for DynamoDB permissions based on resource<a name="access-policy-Contributor_Insights-example3"></a>

The following IAM policy grants permissions to allow the `ListContributorInsights` and `DescribeContributorInsights` actions and denies the `UpdateContributorInsights` action for a specific global secondary index\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "dynamodb:ListContributorInsights",
                "dynamodb:DescribeContributorInsights"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Deny",
            "Action": [
                "dynamodb:UpdateContributorInsights"
            ],
            "Resource": "arn:aws:dynamodb:us-west-2:123456789012:table/Books/index/Author-index"
        }
    ]
}
```

## Using service\-linked roles for CloudWatch Contributor Insights for DynamoDB<a name="contributorinsights-service-linked-roles"></a>

CloudWatch Contributor Insights for DynamoDB uses AWS Identity and Access Management \(IAM\)[ service\-linked roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html#iam-term-service-linked-role)\. A service\-linked role is a unique type of IAM role that is linked directly to CloudWatch Contributor Insights for DynamoDB\. Service\-linked roles are predefined by CloudWatch Contributor Insights for DynamoDB and include all the permissions that the service requires to call other AWS services on your behalf\. 

A service\-linked role makes setting up CloudWatch Contributor Insights for DynamoDB easier because you don’t have to manually add the necessary permissions\. CloudWatch Contributor Insights for DynamoDB defines the permissions of its service\-linked roles, and unless defined otherwise, only CloudWatch Contributor Insights for DynamoDB can assume its roles\. The defined permissions include the trust policy and the permissions policy, and that permissions policy cannot be attached to any other IAM entity\.

For information about other services that support service\-linked roles, see [AWS Services That Work with IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_aws-services-that-work-with-iam.html) and look for the services that have **Yes** in the **Service\-Linked Role** column\. Choose a **Yes** with a link to view the service\-linked role documentation for that service\.

### Service\-linked role permissions for CloudWatch Contributor Insights for DynamoDB<a name="slr-permissions"></a>

CloudWatch Contributor Insights for DynamoDB uses the service\-linked role named **AWSServiceRoleForDynamoDBCloudWatchContributorInsights**\. The purpose of the service\-linked role is to allow Amazon DynamoDB to manage Amazon CloudWatch Contributor Insights rules created for DynamoDB tables and global secondary indexes, on your behalf\.

The `AWSServiceRoleForDynamoDBCloudWatchContributorInsights` service\-linked role trusts the following services to assume the role:
+ `contributorinsights.dynamodb.amazonaws.com `

The role permissions policy allows CloudWatch Contributor Insights for DynamoDB to complete the following actions on the specified resources:
+ Action: `Create and manage Insight Rules` on `DynamoDBContributorInsights`

You must configure permissions to allow an IAM entity \(such as a user, group, or role\) to create, edit, or delete a service\-linked role\. For more information, see [Service\-Linked Role Permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/contributorinsights-service-linked-roles.html#service-linked-role-permissions) in the *IAM User Guide*\.

### Creating a service\-linked role for CloudWatch Contributor Insights for DynamoDB<a name="create-slr"></a>

You don't need to manually create a service\-linked role\. When you enable Contributor Insights in the AWS Management Console, the AWS CLI, or the AWS API, CloudWatch Contributor Insights for DynamoDB creates the service\-linked role for you\. 

If you delete this service\-linked role, and then need to create it again, you can use the same process to recreate the role in your account\. When you enable Contributor Insights, CloudWatch Contributor Insights for DynamoDB creates the service\-linked role for you again\. 

### Editing a service\-linked role for CloudWatch Contributor Insights for DynamoDB<a name="edit-slr"></a>

CloudWatch Contributor Insights for DynamoDB does not allow you to edit the `AWSServiceRoleForDynamoDBCloudWatchContributorInsights` service\-linked role\. After you create a service\-linked role, you cannot change the name of the role because various entities might reference the role\. However, you can edit the description of the role using IAM\. For more information, see [Editing a Service\-Linked Role](https://docs.aws.amazon.com/IAM/latest/UserGuide/contributorinsights-service-linked-roles.html#edit-service-linked-role) in the *IAM User Guide*\.

### Deleting a service\-linked role for CloudWatch Contributor Insights for DynamoDB<a name="delete-slr"></a>

You don't need to manually delete the `AWSServiceRoleForDynamoDBCloudWatchContributorInsights` role\. When you disable Contributor Insights in the AWS Management Console, the AWS CLI, or the AWS API, CloudWatch Contributor Insights for DynamoDB cleans up the resources\.

You can also use the IAM console, the AWS CLI or the AWS API to manually delete the service\-linked role\. To do this, you must first manually clean up the resources for your service\-linked role and then you can manually delete it\.

**Note**  
If the CloudWatch Contributor Insights for DynamoDB service is using the role when you try to delete the resources, then the deletion might fail\. If that happens, wait for a few minutes and try the operation again\.

**To manually delete the service\-linked role using IAM**

Use the IAM console, the AWS CLI, or the AWS API to delete the `AWSServiceRoleForDynamoDBCloudWatchContributorInsights` service\-linked role\. For more information, see [Deleting a Service\-Linked Role](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html) in the *IAM User Guide*\.