# Using IAM with global tables<a name="gt_IAM"></a>

**Important**  
 This documentation is for version 2017\.11\.29 \(Legacy\) of global tables, which should be avoided for new global tables\. Customers should use [Version 2019\.11\.21 \(Current\)](globaltables.V2.md) when possible, as it provides greater flexibility, higher efficiency and consumes less write capacity than 2017\.11\.29 \(Legacy\)\.   
To determine which version you are using, see [Determining the global table version you are using](globaltables.DetermineVersion.md)\. To update existing global tables from version 2017\.11\.29 \(Legacy\) to version 2019\.11\.21 \(Current\), see [Updating global tables](V2globaltables_upgrade.md)\. 

## <a name="globaltables_reqs_bestpractices.permissions-model"></a>

When you create a global table for the first time, Amazon DynamoDB automatically creates an AWS Identity and Access Management \(IAM\) service\-linked role for you\. This role is named `AWSServiceRoleForDynamoDBReplication`, and it allows DynamoDB to manage cross\-Region replication for global tables on your behalf\. Don't delete this service\-linked role\. If you do, then all of your global tables will no longer function\.

For more information about service\-linked roles, see [Using service\-linked roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html) in the *IAM User Guide*\.

To create and maintain global tables in DynamoDB, you must have the `dynamodb:CreateGlobalTable` permission to access each of the following:
+ The replica table that you want to add\.
+ Each existing replica that's already part of the global table\.
+ The global table itself\.

To update the settings \(`UpdateGlobalTableSettings`\) for a global table in DynamoDB, you must have the `dynamodb:UpdateGlobalTable`, `dynamodb:DescribeLimits`, `application-autoscaling:DeleteScalingPolicy`, and `application-autoscaling:DeregisterScalableTarget` permissions\. 

 The `application-autoscaling:DeleteScalingPolicy` and `application-autoscaling:DeregisterScalableTarget` permissions are required when updating an existing scaling policy\. This is so that the global tables service can remove the old scaling policy before attaching the new policy to the table or secondary index\.

If you use an IAM policy to manage access to one replica table, you should apply an identical policy to all other replicas within that global table\. This practice helps you maintain a consistent permissions model across all of the replica tables\.

By using identical IAM policies on all replicas in a global table, you can also avoid granting unintended read and write access to your global table data\. For example, consider a user who has access to only one replica in a global table\. If that user can write to this replica, DynamoDB propagates the write to all of the other replica tables\. In effect, the user can \(indirectly\) write to all of the other replicas in the global table\. This scenario can be avoided by using consistent IAM policies on all of the replica tables\.

## Example: Allow the CreateGlobalTable action<a name="access-policy-gt-example1"></a>

Before you can add a replica to a global table, you must have the `dynamodb:CreateGlobalTable` permission for the global table and for each of its replica tables\.

The following IAM policy grants permissions to allow the `CreateGlobalTable` action on all tables\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": ["dynamodb:CreateGlobalTable"],
            "Resource": "*"
        }
    ]
}
```

## Example: Allow the UpdateGlobalTable, DescribeLimits, application\-autoscaling:DeleteScalingPolicy, and application\-autoscaling:DeregisterScalableTarget actions<a name="access-policy-gt-example2"></a>

To update the settings \(`UpdateGlobalTableSettings`\) for a global table in DynamoDB, you must have the `dynamodb:UpdateGlobalTable`, `dynamodb:DescribeLimits`, `application-autoscaling:DeleteScalingPolicy`, and `application-autoscaling:DeregisterScalableTarget` permissions\. 

The following IAM policy grants permissions to allow the `UpdateGlobalTableSettings` action on all tables\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "dynamodb:UpdateGlobalTable",
                "dynamodb:DescribeLimits",
                "application-autoscaling:DeleteScalingPolicy",
                "application-autoscaling:DeregisterScalableTarget"
            ],
            "Resource": "*"
        }
    ]
}
```

## Example: Allow the CreateGlobalTable action for a specific global table name with replicas allowed in certain regions only<a name="access-policy-gt-example3"></a>

The following IAM policy grants permissions to allow the `CreateGlobalTable` action to create a global table named `Customers` with replicas in two Regions\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "dynamodb:CreateGlobalTable",
            "Resource": [
                "arn:aws:dynamodb::123456789012:global-table/Customers",
                "arn:aws:dynamodb:us-east-1:123456789012:table/Customers",
                "arn:aws:dynamodb:us-west-1:123456789012:table/Customers"
            ]
        }
    ]
}
```