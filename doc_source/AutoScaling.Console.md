# Using the AWS Management Console with DynamoDB auto scaling<a name="AutoScaling.Console"></a>

When you use the AWS Management Console to create a new table, Amazon DynamoDB auto scaling is enabled for that table by default\. You can also use the console to enable auto scaling for existing tables, modify auto scaling settings, or disable auto scaling\.

**Note**  
 For more advanced features like setting scale\-in and scale\-out cooldown times, use the AWS Command Line Interface \(AWS CLI\) to manage DynamoDB auto scaling\. For more information, see [Using the AWS CLI to manage DynamoDB auto scaling](AutoScaling.CLI.md)\.

**Topics**
+ [Before you begin: Granting user permissions for DynamoDB auto scaling](#AutoScaling.Permissions)
+ [Creating a new table with auto scaling enabled](#AutoScaling.Console.NewTable)
+ [Enabling DynamoDB auto scaling on existing tables](#AutoScaling.Console.ExistingTable)
+ [Viewing auto scaling activities on the console](#AutoScaling.Console.ViewingActivities)
+ [Modifying or disabling DynamoDB auto scaling settings](#AutoScaling.Console.Modifying)

## Before you begin: Granting user permissions for DynamoDB auto scaling<a name="AutoScaling.Permissions"></a>

In AWS Identity and Access Management \(IAM\), the AWS managed policy `DynamoDBFullAccess` provides the required permissions for using the DynamoDB console\. However, for DynamoDB auto scaling, users require additional permissions\. 

**Important**  
 To delete an auto scaling\-enabled table, `application-autoscaling:*` permissions are required The AWS managed policy `DynamoDBFullAccess` includes such permissions\. 

To set up a user for DynamoDB console access and DynamoDB auto scaling, create a role and add the **AmazonDynamoDBFullAccess** policy to that role\. Then assign the role to a user\.

To provide access, add permissions to your users, groups, or roles:
+ Users and groups in AWS IAM Identity Center \(successor to AWS Single Sign\-On\):

  Create a permission set\. Follow the instructions in [Create a permission set](https://docs.aws.amazon.com/singlesignon/latest/userguide/howtocreatepermissionset.html) in the *AWS IAM Identity Center \(successor to AWS Single Sign\-On\) User Guide*\.
+ Users managed in IAM through an identity provider:

  Create a role for identity federation\. Follow the instructions in [Creating a role for a third\-party identity provider \(federation\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-idp.html) in the *IAM User Guide*\.
+ IAM users:
  + Create a role that your user can assume\. Follow the instructions in [Creating a role for an IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-user.html) in the *IAM User Guide*\.
  + \(Not recommended\) Attach a policy directly to a user or add a user to a user group\. Follow the instructions in [Adding permissions to a user \(console\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_change-permissions.html#users_change_permissions-add-console) in the *IAM User Guide*\.

## Creating a new table with auto scaling enabled<a name="AutoScaling.Console.NewTable"></a>

**Note**  
DynamoDB auto scaling requires the presence of a service\-linked role \(`AWSServiceRoleForApplicationAutoScaling_DynamoDBTable`\) that performs auto scaling actions on your behalf\. This role is created automatically for you\. For more information, see [Service\-linked roles for Application Auto Scaling](https://docs.aws.amazon.com/autoscaling/application/userguide/application-auto-scaling-service-linked-roles.html) in the *Application Auto Scaling User Guide*\.

**To create a new table with auto scaling enabled**

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. Choose **Create table**\.

1. On the **Create table** page, enter a **Table name** and primary key\.

1. If **Default settings** is selected, the table will be created with auto scaling enabled\.

    Otherwise, for custom settings: 

   1. Select **Customize settings**\.

   1. In the **Read/write capacity settings** section, select **Provisioned** capacity mode and set **Auto scaling** to **On** for **Read capacity**, **Write capacity**, or both\. For each of these, set your desired scaling policy for the table and, optionally, all global secondary indexes of the table\.
      + **Minimum capacity units** – Enter your lower boundary for the auto scaling range\.
      + **Maximum capacity units** – Enter your upper boundary for the auto scaling range\.
      + **Target utilization** – Enter your target utilization percentage for the table\.
**Note**  
If you create a global secondary index for the new table, the index's capacity at time of creation will be the same as your base table's capacity\. You can change the index's capacity in the table's settings after you create the table\.

1. When the settings are as you want them, choose **Create table**\. Your table is created with the auto scaling parameters\.

## Enabling DynamoDB auto scaling on existing tables<a name="AutoScaling.Console.ExistingTable"></a>

**Note**  
DynamoDB auto scaling requires the presence of a service\-linked role \(`AWSServiceRoleForApplicationAutoScaling_DynamoDBTable`\) that performs auto scaling actions on your behalf\. This role is created automatically for you\. For more information, see [Service\-linked roles for Application Auto Scaling](https://docs.aws.amazon.com/autoscaling/application/userguide/application-auto-scaling-service-linked-roles.html)\.

**To enable DynamoDB auto scaling for an existing table**

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. In the navigation pane on the left side of the console, choose **Tables**\.

1. Choose the table that you want to work with and choose the **Additional settings** tab\.

1. In the **Read/write capacity** section, choose **Edit**\.

1. In the **Capacity mode** section, choose **Provisioned**\.

1. In the **Table capacity** section, set **Auto scaling** to **On** for **Read capacity**, **Write capacity**, or both\. For each of these, set your desired scaling policy for the table and, optionally, all global secondary indexes of the table\.
   + **Minimum capacity units** – Enter your lower boundary for the auto scaling range\.
   + **Maximum capacity units** – Enter your upper boundary for the auto scaling range\.
   + **Target utilization** – Enter your target utilization percentage for the table\.
   + **Use the same capacity read/write capacity settings for all global secondary indexes** – Choose whether global secondary indexes should use the same auto scaling policy as the base table\.
**Note**  
For best performance, we recommend that you enable **Use the same read/write capacity settings for all global secondary indexes**\. This option allows DynamoDB auto scaling to uniformly scale all the global secondary indexes on the base table\. This includes existing global secondary indexes, and any others that you create for this table in the future\.  
With this option enabled, you can't set a scaling policy on an individual global secondary index\.

1. When the settings are as you want them, choose **Save**\.

## Viewing auto scaling activities on the console<a name="AutoScaling.Console.ViewingActivities"></a>

As your application drives read and write traffic to your table, DynamoDB auto scaling dynamically modifies the table's throughput settings\. Amazon CloudWatch keeps track of provisioned and consumed capacity, throttled events, latency, and other metrics for all of your DynamoDB tables and secondary indexes\.

To view these metrics in the DynamoDB console, choose the table that you want to work with and choose the **Monitor** tab\. To create a customizable view of table metrics, select **View all in CloudWatch**\.

For more information about CloudWatch monitoring in DynamoDB, see [Monitoring with Amazon CloudWatch](monitoring-cloudwatch.md)\.

## Modifying or disabling DynamoDB auto scaling settings<a name="AutoScaling.Console.Modifying"></a>

You can use the AWS Management Console to modify your DynamoDB auto scaling settings\. To do this, go to the **Additional settings** tab for your table, and choose **Edit** in the **Read/write capacity** section\. For more information about these settings, see [Enabling DynamoDB auto scaling on existing tables](#AutoScaling.Console.ExistingTable)\.