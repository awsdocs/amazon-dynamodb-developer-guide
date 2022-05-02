# Using IAM with Global Tables<a name="V2gt_IAM"></a>


****  

|  | 
| --- |
| There are two versions of DynamoDB global tables available: [Version 2019\.11\.21 \(Current\)](globaltables.V2.md) and [Version 2017\.11\.29](globaltables.V1.md)\. To find out which version you are using, see [Determining the version](globaltables.DetermineVersion.md)\. | 

## <a name="V2globaltables_reqs_bestpractices.permissions-model"></a>

When you create a global table for the first time, Amazon DynamoDB automatically creates an AWS Identity and Access Management \(IAM\) service\-linked role for you\. This role is named `AWSServiceRoleForDynamoDBReplication`, and it allows DynamoDB to manage cross\-Region replication for global tables on your behalf\. Don't delete this service\-linked role\. If you do, all of your global tables will no longer function\.

For more information about service\-linked roles, see [Using Service\-Linked Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html) in the *IAM User Guide*\.

To create replica tables in DynamoDB, you must have the following permissions in the source region\.
+ `dynamodb:UpdateTable`

To create replica tables in DynamoDB, you must have the following permissions in destination regions\.
+ `dynamodb:CreateTable`
+ `dynamodb:CreateTableReplica`
+ `dynamodb:Scan`
+ `dynamodb:Query`
+ `dynamodb:UpdateItem`
+ `dynamodb:PutItem`
+ `dynamodb:GetItem`
+ `dynamodb:DeleteItem`
+ `dynamodb:BatchWriteItem`

To delete replica tables in DynamoDB, you must have the following permissions in the destination regions\.
+ `dynamodb:DeleteTable`
+ `dynamodb:DeleteTableReplica`

To update replica auto\-scaling policy through `UpdateTableReplicaAutoScaling`, you must have the following permissions in all Regions where table replicas exist
+ `application-autoscaling:DeleteScalingPolicy`
+ `application-autoscaling:DeleteScheduledAction`
+ `application-autoscaling:DeregisterScalableTarget`
+ `application-autoscaling:DescribeScalableTargets`
+ `application-autoscaling:DescribeScalingActivities`
+ `application-autoscaling:DescribeScalingPolicies`
+ `application-autoscaling:DescribeScheduledActions`
+ `application-autoscaling:PutScalingPolicy`
+ `application-autoscaling:PutScheduledAction`
+ `application-autoscaling:RegisterScalableTarget`

To use `UpdateTimeToLive` you must have permission for `dynamodb:UpdateTimeToLive` in all Regions where table replicas exist\.

## Example: Add Replica<a name="V2access-policy-gt-example1"></a>

The following IAM policy grants permissions to allow you to add replicas to a global table\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "dynamodb:CreateTable",
                "dynamodb:DescribeTable",
                "dynamodb:UpdateTable",
                "dynamodb:CreateTableReplica",
                "iam:CreateServiceLinkedRole"
            ],
            "Resource": "*"
        }
    ]
}
```

## Example: Update AutoScaling policy<a name="V2access-policy-gt-example2"></a>

The following IAM policy grants permissions to allow you to update replica auto\-scaling policy\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "application-autoscaling:RegisterScalableTarget",
                "application-autoscaling:DeleteScheduledAction",
                "application-autoscaling:DescribeScalableTargets",
                "application-autoscaling:DescribeScalingActivities",
                "application-autoscaling:DescribeScalingPolicies",
                "application-autoscaling:PutScalingPolicy",
                "application-autoscaling:DescribeScheduledActions",
                "application-autoscaling:DeleteScalingPolicy",
                "application-autoscaling:PutScheduledAction",
                "application-autoscaling:DeregisterScalableTarget"
            ],
            "Resource": "*"
        }
    ]
}
```

## Example: Allow Replica Creations for a Specific Table Name and Regions<a name="V2access-policy-gt-example3"></a>

The following IAM policy grants permissions to allow table and replica creation for `Customers` table with replicas in three Regions\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "dynamodb:CreateTable",
                "dynamodb:DescribeTable",                
                "dynamodb:UpdateTable"
            ],
            
            "Resource": [
                "arn:aws:dynamodb:us-east-1:123456789012:table/Customers",
                "arn:aws:dynamodb:us-west-1:123456789012:table/Customers",
                "arn:aws:dynamodb:eu-east-2:123456789012:table/Customers"
            ]
        }
    ]
}
```