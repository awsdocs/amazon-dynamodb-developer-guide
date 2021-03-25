# Using Service\-Linked IAM Roles for DAX<a name="using-service-linked-roles"></a>

Amazon DynamoDB Accelerator \(DAX\) uses AWS Identity and Access Management \(IAM\) [service\-linked roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html#iam-term-service-linked-role)\. A service\-linked role is a unique type of IAM role that is linked directly to DAX\. Service\-linked roles are predefined by DAX and include all the permissions that the service requires to call other AWS services on your behalf\. 

A service\-linked role makes setting up DAX easier because you don’t have to manually add the necessary permissions\. DAX defines the permissions of its service\-linked roles, and unless defined otherwise, only DAX can assume its roles\. The defined permissions include the trust policy and the permissions policy\. That permissions policy can't be attached to any other IAM entity\.

You can delete the roles only after first deleting their related resources\. This protects your DAX resources because you can't inadvertently remove permission to access the resources\.

For information about other services that support service\-linked roles, see [AWS Services That Work with IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_aws-services-that-work-with-iam.html) in the *IAM User Guide*\. Look for the services that have **Yes** in the **Service\-linked roles** column\. Choose a **Yes** link to view the service\-linked role documentation for that service\.

**Topics**
+ [Service\-Linked Role Permissions for DAX](#service-linked-role-permissions)
+ [Creating a Service\-Linked Role for DAX](#create-service-linked-role)
+ [Editing a Service\-Linked Role for DAX](#edit-service-linked-role)
+ [Deleting a Service\-Linked Role for DAX](#delete-service-linked-role)

## Service\-Linked Role Permissions for DAX<a name="service-linked-role-permissions"></a>

DAX uses the service\-linked role named `AWSServiceRoleForDAX`\. This role allows DAX to call services on behalf of your DAX cluster\.

**Important**  
The `AWSServiceRoleForDAX` service\-linked role makes it easier for you to set up and maintain a DAX cluster\. However, you must still grant each cluster access to DynamoDB before you can use it\. For more information, see [DAX Access Control](DAX.access-control.md)\.

The `AWSServiceRoleForDAX` service\-linked role trusts the following services to assume the role:
+ `dax.amazonaws.com`

The role permissions policy allows DAX to complete the following actions on the specified resources:
+ Actions on `ec2`:
  + `AuthorizeSecurityGroupIngress`
  + `CreateNetworkInterface`
  + `CreateSecurityGroup`
  + `DeleteNetworkInterface`
  + `DeleteSecurityGroup`
  + `DescribeAvailabilityZones`
  + `DescribeNetworkInterfaces`
  + `DescribeSecurityGroups`
  + `DescribeSubnets`
  + `DescribeVpcs`
  + `ModifyNetworkInterfaceAttribute`
  + `RevokeSecurityGroupIngress`

You must configure permissions to allow an IAM entity \(such as a user, group, or role\) to create, edit, or delete a service\-linked role\. For more information, see [Service\-Linked Role Permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#service-linked-role-permissions) in the *IAM User Guide*\.

**To allow an IAM entity to create AWSServiceRoleForDAX service\-linked roles**

 Add the following policy statement to the permissions for that IAM entity\.

```
{
    "Effect": "Allow",
    "Action": [
        "iam:CreateServiceLinkedRole"
    ],
    "Resource": "*",
    "Condition": {"StringLike": {"iam:AWSServiceName": "dax.amazonaws.com"}}
}
```

## Creating a Service\-Linked Role for DAX<a name="create-service-linked-role"></a>

You don't need to manually create a service\-linked role\. When you create a cluster, DAX creates the service\-linked role for you\. 

**Important**  
If you were using the DAX service before February 28, 2018, when it began supporting service\-linked roles, DAX created the `AWSServiceRoleForDAX` role in your account\. For more information, see [A New Role Appeared in My AWS Account](https://docs.aws.amazon.com/IAM/latest/UserGuide/troubleshoot_roles.html#troubleshoot_roles_new-role-appeared) in the *IAM User Guide*\.

If you delete this service\-linked role and then need to create it again, you can use the same process to re\-create the role in your account\. When you create an instance or a cluster, DAX creates the service\-linked role for you again\.

## Editing a Service\-Linked Role for DAX<a name="edit-service-linked-role"></a>

DAX does not allow you to edit the `AWSServiceRoleForDAX` service\-linked role\. After you create a service\-linked role, you cannot change the name of the role because various entities might reference the role\. However, you can edit the description of the role using IAM\. For more information, see [Editing a Service\-Linked Role](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#edit-service-linked-role) in the *IAM User Guide*\.

## Deleting a Service\-Linked Role for DAX<a name="delete-service-linked-role"></a>

If you no longer need to use a feature or service that requires a service\-linked role, we recommend that you delete that role\. That way you don’t have an unused entity that is not actively monitored or maintained\. However, you must delete all of your DAX clusters before you can delete the service\-linked role\.

### Cleaning Up a Service\-Linked Role<a name="service-linked-role-review-before-delete"></a>

Before you can use IAM to delete a service\-linked role, you must first confirm that the role has no active sessions and remove any resources used by the role\.

**To check whether the service\-linked role has an active session in the IAM console**

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane of the IAM console, choose **Roles**\. Then choose the name \(not the check box\) of the **AWSServiceRoleForDAX** role\.

1. On the **Summary** page for the selected role, choose the **Access Advisor** tab\.

1. On the **Access Advisor** tab, review recent activity for the service\-linked role\.
**Note**  
If you are unsure whether DAX is using the `AWSServiceRoleForDAX` role, you can try to delete the role\. If the service is using the role, the deletion fails, and you can view the Regions where the role is being used\. If the role is being used, you must delete your DAX clusters before you can delete the role\. You can't revoke the session for a service\-linked role\. 

If you want to remove the `AWSServiceRoleForDAX` role, you must first delete all of your DAX clusters\. 

#### Deleting All of Your DAX Clusters<a name="delete-service-linked-role.clusters"></a>

Use one of these procedures to delete each of your DAX clusters\. 

**To delete a DAX cluster \(console\)**

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. In the navigation pane, under **DAX**, choose **Clusters**\.

1. Choose **Actions**, and then choose **Delete**\.

1. In the **Delete cluster confirmation** box, choose **Delete**\.

**To delete a DAX cluster \(AWS CLI\)**  
See [delete\-cluster](https://docs.aws.amazon.com/cli/latest/reference/dax/delete-cluster.html) in the *AWS CLI Command Reference*\.

**To delete a DAX cluster \(API\)**  
See [DeleteCluster](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_DeleteCluster.html) in the *Amazon DynamoDB API Reference*\.

#### Deleting the Service\-Linked Role<a name="delete-service-linked-role.slr"></a>

**To manually delete the service\-linked role using IAM**

Use the IAM console, the IAM CLI, or the IAM API to delete the `AWSServiceRoleForDAX` service\-linked role\. For more information, see [Deleting a Service\-Linked Role](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#delete-service-linked-role) in the *IAM User Guide*\.