# DynamoDB API Permissions: Actions, Resources, and Conditions Reference<a name="api-permissions-reference"></a>

When you are setting up [Access Control](authentication-and-access-control.md#access-control) and writing a permissions policy that you can attach to an IAM identity \(identity\-based policies\), you can use the list of [Actions, resources, and condition keys for Amazon DynamoDB](https://docs.aws.amazon.com/service-authorization/latest/reference/list_amazondynamodb.html) in the *IAM User Guide* as a reference\. The page lists each DynamoDB API operation, the corresponding actions for which you can grant permissions to perform the action, and the AWS resource for which you can grant the permissions\. You specify the actions in the policy's `Action` field, and you specify the resource value in the policy's `Resource` field\.

You can use AWS\-wide condition keys in your DynamoDB policies to express conditions\. For a complete list of AWS\-wide keys, see the [IAM JSON policy elements reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#AvailableKeys) in the *IAM User Guide*\.

In addition to the AWS\-wide condition keys, DynamoDB has its own specific keys that you can use in conditions\. For more information, see [Using IAM Policy Conditions for Fine\-Grained Access Control](specifying-conditions.md)\.

## Related Topics<a name="w615aac28c15b9c17c11"></a>
+ [Access Control](authentication-and-access-control.md#access-control)
+ [Using IAM Policy Conditions for Fine\-Grained Access Control](specifying-conditions.md)