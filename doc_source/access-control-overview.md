# Overview of Managing Access Permissions to Your Amazon DynamoDB Resources<a name="access-control-overview"></a>

Every AWS resource is owned by an AWS account, and permissions to create or access a resource are governed by permissions policies\. An account administrator can attach permissions policies to IAM identities \(that is, users, groups, and roles\), and some services \(such as AWS Lambda\) also support attaching permissions policies to resources\.

**Note**  
An *account administrator* \(or administrator user\) is a user with administrator privileges\. For more information, see [IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) in the *IAM User Guide*\.

When granting permissions, you decide who is getting the permissions, the resources they get permissions for, and the specific actions that you want to allow on those resources\.

**Topics**
+ [DynamoDB Resources and Operations](#access-control-resources)
+ [Understanding Resource Ownership](#access-control-resource-ownership)
+ [Managing Access to Resources](#access-control-manage-access-intro)
+ [Specifying Policy Elements: Actions, Effects, and Principals](#specify-policy-elements)
+ [Specifying Conditions in a Policy](#specifying-conditions-overview)

## DynamoDB Resources and Operations<a name="access-control-resources"></a>



 In DynamoDB, the primary resources are *tables*\. DynamoDB also supports additional resource types, *indexes*, and *streams*\. However, you can create indexes and streams only in the context of an existing DynamoDB table\. These are referred to as *subresources*\. 

These resources and subresources have unique Amazon Resource Names \(ARNs\) associated with them, as shown in the following table\. 


****  

| Resource Type | ARN Format | 
| --- | --- | 
| Table |  arn:aws:dynamodb:region:account\-id:table/table\-name  | 
| Index |  arn:aws:dynamodb:region:account\-id:table/table\-name/index/index\-name | 
| Stream |  arn:aws:dynamodb:region:account\-id:table/table\-name/stream/stream\-label | 

DynamoDB provides a set of operations to work with DynamoDB resources\. For a list of available operations, see Amazon DynamoDB [Actions](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Operations.html)\.

## Understanding Resource Ownership<a name="access-control-resource-ownership"></a>

The AWS account owns the resources that are created in the account, regardless of who created the resources\. Specifically, the resource owner is the AWS account of the [principal entity](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html) \(that is, the AWS account root user, an IAM user, or an IAM role\) that authenticates the resource creation request\. The following examples illustrate how this works:
+ If you use your AWS account root user credentials to create a table, your AWS account is the owner of the resource \(in DynamoDB, the resource is a table\)\.
+ If you create an IAM user in your AWS account and grant permissions to create a table to that user, the user can create a table\. However, your AWS account, to which the user belongs, owns the table resource\.
+ If you create an IAM role in your AWS account with permissions to create a table, anyone who can assume the role can create a table\. Your AWS account, to which the role belongs, owns the table resource\. 

## Managing Access to Resources<a name="access-control-manage-access-intro"></a>

A *permissions policy* describes who has access to what\. The following section explains the available options for creating permissions policies\.

**Note**  
This section discusses using IAM in the context of DynamoDB\. It doesn't provide detailed information about the IAM service\. For complete IAM documentation, see [What Is IAM?](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html) in the *IAM User Guide*\. For information about IAM policy syntax and descriptions, see [AWS IAM Policy Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html) in the *IAM User Guide*\.

Policies attached to an IAM identity are referred to as *identity\-based* policies \(IAM policies\)\. Policies attached to a resource are referred to as *resource\-based* policies\. DynamoDB supports only identity\-based policies \(IAM policies\)\.

**Topics**
+ [Identity\-Based Policies \(IAM Policies\)](#access-control-manage-access-identity-based)
+ [Resource\-Based Policies](#access-control-manage-access-resource-based)

### Identity\-Based Policies \(IAM Policies\)<a name="access-control-manage-access-identity-based"></a>

You can attach policies to IAM identities\. For example, you can do the following:
+ **Attach a permissions policy to a user or a group in your account** – To grant a user permissions to create an Amazon DynamoDB resource, such as a table, you can attach a permissions policy to a user or group that the user belongs to\.
+ **Attach a permissions policy to a role \(grant cross\-account permissions\)** – You can attach an identity\-based permissions policy to an IAM role to grant cross\-account permissions\. For example, the administrator in account A can create a role to grant cross\-account permissions to another AWS account \(for example, account B\) or an AWS service as follows:

  1. Account A administrator creates an IAM role and attaches a permissions policy to the role that grants permissions on resources in account A\.

  1. Account A administrator attaches a trust policy to the role identifying account B as the principal who can assume the role\. 

  1. Account B administrator can then delegate permissions to assume the role to any users in account B\. Doing this allows users in account B to create or access resources in account A\. The principal in the trust policy can also be an AWS service principal if you want to grant an AWS service permissions to assume the role\.

  For more information about using IAM to delegate permissions, see [Access Management](https://docs.aws.amazon.com/IAM/latest/UserGuide/access.html) in the *IAM User Guide*\.

The following is an example policy that grants permissions for one DynamoDB action \(`dynamodb:ListTables`\)\. The wildcard character \(\*\) in the `Resource` value means that you can use this action to obtain the names of all the tables owned by the AWS account in the current AWS Region\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "ListTables",
            "Effect": "Allow",
            "Action": [
                "dynamodb:ListTables"
            ],
            "Resource": "*"
        }
    ]
}
```

For more information about using identity\-based policies with DynamoDB, see [Using Identity\-Based Policies \(IAM Policies\) with Amazon DynamoDB](using-identity-based-policies.md)\. For more information about users, groups, roles, and permissions, see [Identities \(Users, Groups, and Roles\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html) in the *IAM User Guide*\. 

### Resource\-Based Policies<a name="access-control-manage-access-resource-based"></a>

Other services, such as Amazon S3, also support resource\-based permissions policies\. For example, you can attach a policy to an S3 bucket to manage access permissions to that bucket\. DynamoDB doesn't support resource\-based policies\.

## Specifying Policy Elements: Actions, Effects, and Principals<a name="specify-policy-elements"></a>

For each DynamoDB resource, the service defines a set of API operations\. To grant permissions for these API operations, DynamoDB defines a set of actions that you can specify in a policy\. Some API operations can require permissions for more than one action in order to perform the API operation\. For more information about resources and API operations, see [DynamoDB Resources and Operations](#access-control-resources) and DynamoDB [Actions](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Operations.html)\.

The following are the most basic policy elements:
+ **Resource** – You use an Amazon Resource Name \(ARN\) to identify the resource that the policy applies to\. For more information, see [DynamoDB Resources and Operations](#access-control-resources)\.
+ **Action** – You use action keywords to identify resource operations that you want to allow or deny\. For example, `dynamodb:Query` allows the user permissions to perform the DynamoDB `Query` operation\.
+ **Effect** – You specify the effect, either allow or deny, when the user requests the specific action\. If you don't explicitly grant access to \(allow\) a resource, access is implicitly denied\. You can also explicitly deny access to a resource, which you might do to make sure that a user cannot access it, even if a different policy grants access\.
+ **Principal** – In identity\-based policies \(IAM policies\), the user that the policy is attached to is the implicit principal\. For resource\-based policies, you specify the user, account, service, or other entity that you want to receive permissions \(applies to resource\-based policies only\)\. DynamoDB doesn't support resource\-based policies\.

To learn more about IAM policy syntax and descriptions, see [AWS IAM Policy Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html) in the *IAM User Guide*\.

For a table showing all of the Amazon DynamoDB API operations and the resources that they apply to, see [DynamoDB API Permissions: Actions, Resources, and Conditions Reference](api-permissions-reference.md)\.

## Specifying Conditions in a Policy<a name="specifying-conditions-overview"></a>

When you grant permissions, you can use the access policy language to specify the conditions when a policy should take effect\. For example, you might want a policy to be applied only after a specific date\. For more information about specifying conditions in a policy language, see [Condition](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#Condition) in the *IAM User Guide*\.

To express conditions, you use predefined condition keys\. There are AWS\-wide condition keys and DynamoDB–specific keys that you can use as appropriate\. For a complete list of AWS\-wide keys, see [Available Keys for Conditions](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#AvailableKeys) in the *IAM User Guide*\. For a complete list of DynamoDB–specific keys, see [Using IAM Policy Conditions for Fine\-Grained Access Control](specifying-conditions.md)\.