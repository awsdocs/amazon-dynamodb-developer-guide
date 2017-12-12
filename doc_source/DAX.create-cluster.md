# Creating a DAX Cluster<a name="DAX.create-cluster"></a>

This section walks you through first\-time setup and usage of DAX in your default Amazon VPC environment\. You can create your first DAX cluster using either the AWS Command Line Interface \(AWS CLI\) or the AWS Management Console\.

After you have created your DAX cluster, you will be able to access it from an Amazon EC2 instance running in the same Amazon VPC\. You will then be able to use your DAX cluster with an application program\. \(For more information, see [Using the DAX Client in an Application](DAX.client.md)\.\)


+ [Creating a DAX Service Role](#DAX.create-cluster.iam-permissions)
+ [AWS CLI](DAX.create-cluster.cli.md)
+ [AWS Management Console](DAX.create-cluster.console.md)

## Creating a DAX Service Role<a name="DAX.create-cluster.iam-permissions"></a>

In order for your DAX cluster to access DynamoDB tables on your behalf, you will need to create a service role\. A *service role* is an IAM role that authorizes an AWS service to act on your behalf\. The service role will allow DAX to access your DynamoDB tables, as if you were accessing those tables yourself\. You need to create the service role before you can create the DAX cluster\.

If you are using the AWS Management Console, the workflow for creating a cluster checks for the presence of a pre\-existing DAX service role; if none is found, the console creates a new service role for you\. For more information, see [Step 2: Create a DAX Cluster](DAX.create-cluster.console.create-cluster.md) in [AWS Management Console](DAX.create-cluster.console.md)\.

If you are using the AWS CLI, you will need to specify a DAX service role that you have created previously\. Otherwise, you will need to create a new service role beforehand\. For more information, see [Step 1: Create a Service Role for DAX](DAX.create-cluster.cli.create-service-role.md) in [AWS CLI](DAX.create-cluster.cli.md)\.

### Permissions Required for Service Role Creation<a name="DAX.create-cluster.iam-permissions.required"></a>

The AWS\-managed *AdministratorAccess* policy provides all of the permissions needed for creating a DAX cluster, and for creating a service role\. If your IAM user has *AdministratorAccess* attached, then no further action is needed\. 

Otherwise, you will need to add the following permissions to your IAM policy so that your IAM user can create the service role:

+ `iam:CreateRole`

+ `iam:CreatePolicy`

+ `iam:AttachRolePolicy`

+ `iam:PassRole`

You should attach these permissions to the user that is attempting to perform the action\.

**Note**  
The `iam:CreateRole`, i`am:CreatePolicy`, `iam:AttachPolicy` and `iam:PassRole` permissions are not included in the AWS\-managed policies for DynamoDB\. This is by design, because these permissions provide the possibility of privilege escalation: A user could use these permissions to create a new administrator policy, and then attach that policy to an existing role\. For this reason, you \(the administrator of your DAX cluster\) must explicitly add these permissions to your policy\. 

### Troubleshooting<a name="DAX.create-cluster.iam-permissions.troubleshooting"></a>

If your user policy is missing the `iam:CreateRole`, `iam:CreatePolicy`, and `iam:AttachPolicy` permissions, you will encounter error messages\. The following table shows these messages, and how to correct the problems\.


****  

| If you see this error message\.\.\. | Do the following: | 
| --- | --- | 
| User: arn:aws:iam::accountID:user/userName is not authorized to perform: iam:CreateRole on resource: arn:aws:iam::accountID:role/service\-role/roleName  | Add iam:CreateRole to your user policy\. | 
| User: arn:aws:iam::accountID:user/userName is not authorized to perform: iam:CreatePolicy on resource: policy policyName |  Add iam:CreatePolicy to your user policy\. | 
| User: arn:aws:iam::accountID:user/userName is not authorized to perform: iam:AttachRolePolicy on resource: role daxServiceRole | Add iam:AttachRolePolicy to your user policy\. | 

For more information about IAM policies required for DAX cluster administration, see [DAX Access Control](DAX.access-control.md)\.