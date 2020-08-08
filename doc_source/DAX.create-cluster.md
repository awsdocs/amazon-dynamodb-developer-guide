# Creating a DAX Cluster<a name="DAX.create-cluster"></a>

This section walks you through the first\-time setup and usage of Amazon DynamoDB Accelerator \(DAX\) in your default Amazon Virtual Private Cloud \(Amazon VPC\) environment\. You can create your first DAX cluster using either the AWS Command Line Interface \(AWS CLI\) or the AWS Management Console\.

After you create your DAX cluster, you can access it from an Amazon EC2 instance running in the same VPC\. You can then use your DAX cluster with an application program\. For more information, see [Developing with the DynamoDB Accelerator \(DAX\) Client](DAX.client.md)\.

**Topics**
+ [Creating an IAM Service Role for DAX to Access DynamoDB](#DAX.create-cluster.iam-permissions)
+ [Creating a DAX Cluster Using the AWS CLI](DAX.create-cluster.cli.md)
+ [Creating a DAX Cluster Using the AWS Management Console](DAX.create-cluster.console.md)

## Creating an IAM Service Role for DAX to Access DynamoDB<a name="DAX.create-cluster.iam-permissions"></a>

For your DAX cluster to access DynamoDB tables on your behalf, you must create a *service role*\. A service role is an AWS Identity and Access Management \(IAM\) role that authorizes an AWS service to act on your behalf\. The service role allows DAX to access your DynamoDB tables, as if you were accessing those tables yourself\. You must create the service role before you can create the DAX cluster\.

If you are using the console, the workflow for creating a cluster checks for the presence of a pre\-existing DAX service role\. If none is found, the console creates a new service role for you\. For more information, see [Step 2: Create a DAX Cluster Using the AWS Management Console](DAX.create-cluster.console.create-cluster.md)\.

If you are using the AWS CLI, you must specify a DAX service role that you have created previously\. Otherwise, you need to create a new service role beforehand\. For more information, see [Step 1: Create an IAM Service Role for DAX to Access DynamoDB Using the AWS CLI](DAX.create-cluster.cli.create-service-role.md)\.

### Permissions Required to Create a Service Role<a name="DAX.create-cluster.iam-permissions.required"></a>

The AWS managed `AdministratorAccess` policy provides all the permissions needed for creating a DAX cluster and a service role\. If your IAM user has `AdministratorAccess` attached, no further action is needed\.

Otherwise, you must add the following permissions to your IAM policy so that your IAM user can create the service role:
+ `iam:CreateRole`
+ `iam:CreatePolicy`
+ `iam:AttachRolePolicy`
+ `iam:PassRole`

Attach these permissions to the user who is trying to perform the action\.

**Note**  
The `iam:CreateRole`, `iam:CreatePolicy`, `iam:AttachRolePolicy`, and `iam:PassRole` permissions are not included in the AWS managed policies for DynamoDB\. This is by design because these permissions provide the possibility of privilege escalation: That is, a user could use these permissions to create a new administrator policy and then attach that policy to an existing role\. For this reason, you \(the administrator of your DAX cluster\) must explicitly add these permissions to your policy\.

### Troubleshooting<a name="DAX.create-cluster.iam-permissions.troubleshooting"></a>

If your user policy is missing the `iam:CreateRole`, `iam:CreatePolicy`, and `iam:AttachPolicy` permissions, you will get error messages\. The following table lists these messages and describes how to correct the problems\.


****  

| If you see this error message\.\.\. | Do the following: | 
| --- | --- | 
| User: arn:aws:iam::accountID:user/userName is not authorized to perform: iam:CreateRole on resource: arn:aws:iam::accountID:role/service\-role/roleName  | Add iam:CreateRole to your user policy\. | 
| User: arn:aws:iam::accountID:user/userName is not authorized to perform: iam:CreatePolicy on resource: policy policyName |  Add iam:CreatePolicy to your user policy\. | 
| User: arn:aws:iam::accountID:user/userName is not authorized to perform: iam:AttachRolePolicy on resource: role daxServiceRole | Add iam:AttachRolePolicy to your user policy\. | 

For more information about the IAM policies required for DAX cluster administration, see [DAX Access Control](DAX.access-control.md)\.