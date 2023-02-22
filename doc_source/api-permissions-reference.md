# DynamoDB API permissions: Actions, resources, and conditions reference<a name="api-permissions-reference"></a>

When you are setting up  [Identity and Access Management for Amazon DynamoDB](security-iam.md) and writing a permissions policy that you can attach to an IAM identity \(identity\-based policies\), you can use the list of [Actions, resources, and condition keys for Amazon DynamoDB](https://docs.aws.amazon.com/service-authorization/latest/reference/list_amazondynamodb.html) in the *IAM User Guide* as a reference\. The page lists each DynamoDB API operation, the corresponding actions for which you can grant permissions to perform the action, and the AWS resource for which you can grant the permissions\. You specify the actions in the policy's `Action` field, and you specify the resource value in the policy's `Resource` field\.

You can use AWS\-wide condition keys in your DynamoDB policies to express conditions\. For a complete list of AWS\-wide keys, see the [IAM JSON policy elements reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#AvailableKeys) in the *IAM User Guide*\.

In addition to the AWS\-wide condition keys, DynamoDB has its own specific keys that you can use in conditions\. For more information, see [Using IAM policy conditions for fine\-grained access control](specifying-conditions.md)\.

## Related topics<a name="w120aac28c15c15c13b9"></a>
+  [Identity and Access Management for Amazon DynamoDB](security-iam.md)
+ [Using IAM policy conditions for fine\-grained access control](specifying-conditions.md)