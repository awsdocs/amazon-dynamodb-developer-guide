# Using IAM with Global Tables<a name="gt_IAM"></a>

## <a name="globaltables_reqs_bestpractices.permissions-model"></a>

When you create a global table for the first time, DynamoDB automatically creates an IAM service\-linked role for you\. This role is named `AWSServiceRoleForDynamoDBReplication`, and it allows DynamoDB to manage cross\-region replication for global tables on your behalf\.

Do not delete this service\-linked role\. If you do, then all of your global tables will no longer function\.

\(For more information about service\-linked roles, see [Using Service\-Linked Roles](http://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html) in the IAM User Guide\.\)

To create and maintain global tables in DynamoDB, you must have the `dynamodb:CreateGlobalTable` permission to access each of the following:

+ The replica table you want to add\.

+ Each existing replica that's already part of the global table\.

+ The global table itself\.

If you use an IAM policy to manage access to one replica table, then you should apply an identical policy to all of the other replicas within that global table\. This practice will help you maintain a consistent permissions model across all of the replica tables\.

By using identical IAM policies on all replicas in a global table, you can also avoid granting unintended read and write access to your global table data\. For example, consider a user who has access to only one replica in a global table\. If that user can write to this replica, then DynamoDB will propagate the write to all of the other replica tables\. In effect, the user can \(indirectly\) write to all of the other replicas in the global table\. This scenario can be avoided by using consistent IAM policies on all of the replica tables\.

## Example: Allow the `CreateGlobalTable` Action<a name="access-policy-gt-example1"></a>

Before you can add a replica to a global table, you must have the `dynamodb:CreateGlobalTable` permission for the global table and for each of its replica tables\.

The following IAM policy grants permissions to allow the `CreateGlobalTable` action on all tables:

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