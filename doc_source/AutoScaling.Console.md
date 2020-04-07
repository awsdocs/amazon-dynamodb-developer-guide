# Using the AWS Management Console with DynamoDB Auto Scaling<a name="AutoScaling.Console"></a>

When you use the AWS Management Console to create a new table, Amazon DynamoDB auto scaling is enabled for that table by default\. You can also use the console to enable auto scaling for existing tables, modify auto scaling settings, or disable auto scaling\.

**Note**  
 For more advanced features like setting scale\-in and scale\-out cooldown times, use the AWS Command Line Interface \(AWS CLI\) to manage DynamoDB auto scaling\. For more information, see [Using the AWS CLI to Manage DynamoDB Auto Scaling](AutoScaling.CLI.md)\.

**Topics**
+ [Before You Begin: Granting User Permissions for DynamoDB Auto Scaling](#AutoScaling.Permissions)
+ [Creating a New Table with Auto Scaling Enabled](#AutoScaling.Console.NewTable)
+ [Enabling DynamoDB Auto Scaling on Existing Tables](#AutoScaling.Console.ExistingTable)
+ [Viewing Auto Scaling Activities on the Console](#AutoScaling.Console.ViewingActivities)
+ [Modifying or Disabling DynamoDB Auto Scaling Settings](#AutoScaling.Console.Modifying)

## Before You Begin: Granting User Permissions for DynamoDB Auto Scaling<a name="AutoScaling.Permissions"></a>

In AWS Identity and Access Management \(IAM\), the AWS managed policy `DynamoDBFullAccess` provides the required permissions for using the DynamoDB console\. However, for DynamoDB auto scaling, IAM users require additional privileges\. 

**Important**  
 `application-autoscaling:*` permissions are required to delete an auto scaling\-enabled table\. The AWS managed policy `DynamoDBFullAccess` includes such permissions\. 

To set up an IAM user for DynamoDB console access and DynamoDB auto scaling, add the following policy\.

**To attach the AmazonDynamoDBFullAccess policy**

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. On the IAM console dashboard, choose **Users**, and then choose your IAM user from the list\.

1. On the **Summary** page, choose **Add permissions**\.

1. Choose **Attach existing policies directly**\.

1. From the list of policies, choose **AmazonDynamoDBFullAccess**, and then choose **Next: Review**\.

1. Choose **Add permissions**\.

## Creating a New Table with Auto Scaling Enabled<a name="AutoScaling.Console.NewTable"></a>

**Note**  
DynamoDB auto scaling requires the presence of a service linked role \(`AWSServiceRoleForApplicationAutoScaling_DynamoDBTable`\) that performs auto scaling actions on your behalf\. This role is created automatically for you\. For more information, see [Service\-Linked Roles for Application Auto Scaling](https://docs.aws.amazon.com/autoscaling/application/userguide/application-auto-scaling-service-linked-roles.html) in the *Application Auto Scaling User Guide*\. 

**To create a new table with auto scaling enabled**

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. Choose **Create Table**\. 

1. On the **Create DynamoDB table** page, enter a **Table name** and **Primary key** details\.

1.  Ensure that **Use default settings** is selected\. \(Your AWS account already has *AWSServiceRoleForApplicationAutoScaling\_DynamoDBTable*\.\) 

    Otherwise, for custom settings: 

   1. Clear **Use default settings**\.

   1. In the **Auto scaling** section, set the parameter settings and ensure that `AWSServiceRoleForApplicationAutoScaling_DynamoDBTable` is selected\.

1. When the settings are as you want them, choose **Create**\. Your table is created with the auto scaling parameters\.

## Enabling DynamoDB Auto Scaling on Existing Tables<a name="AutoScaling.Console.ExistingTable"></a>

**Note**  
DynamoDB auto scaling requires the presence of a service linked role \(`AWSServiceRoleForApplicationAutoScaling_DynamoDBTable`\) that performs auto scaling actions on your behalf\. This role is created automatically for you\. For more information, see [Service\-Linked Roles for Application Auto Scaling](https://docs.aws.amazon.com/autoscaling/application/userguide/application-auto-scaling-service-linked-roles.html)\.  
If you have never used DynamoDB auto scaling before, see [Creating a New Table with Auto Scaling Enabled](#AutoScaling.Console.NewTable)\.

**To enable DynamoDB auto scaling for an existing table**

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. Choose the table that you want to work with, and then choose **Capacity**\.

1. In the **Auto Scaling** section, do the following:

   1. Select **Read capacity**, **Write capacity**, or both\. \(For write capacity, you can choose **Same settings as read**\.\) For each of these, do the following:
     + **Target utilization**—Enter your target utilization percentage for the table\.
     + **Minimum provisioned capacity**—Enter your lower boundary for the auto scaling range\.
     + **Maximum provisioned capacity**—Enter your upper boundary for the auto scaling range\.
     + **Apply same settings to global secondary indexes**—Keep this option at its default setting \(enabled\)\. 
**Note**  
For best performance, we recommend that you enable **Apply same settings to global secondary indexes**\. This option allows DynamoDB auto scaling to uniformly scale all the global secondary indexes on the base table\. This includes existing global secondary indexes, and any others that you create for this table in the future\.  
With this option enabled, you can't set a scaling policy on an individual global secondary index\.

     \(For **Write capacity**, you can choose **Same settings as read**\.\)

     In the **IAM Role** section, ensure that **AWSServiceRoleForApplicationAutoScaling\_DynamoDBTable** is selected\.

1. When the settings are as you want them, choose **Save**\.

## Viewing Auto Scaling Activities on the Console<a name="AutoScaling.Console.ViewingActivities"></a>

As your application drives read and write traffic to your table, DynamoDB auto scaling dynamically modifies the table's throughput settings\.

To view these auto scaling activities on the DynamoDB console, choose the table that you want to work with\. Choose **Capacity**, and then expand the **Scaling activities** section\. When your table's throughput settings are modified, you see informational messages here\.

## Modifying or Disabling DynamoDB Auto Scaling Settings<a name="AutoScaling.Console.Modifying"></a>

You can use the AWS Management Console to modify your DynamoDB auto scaling settings\. To do this, go to the **Capacity** tab for your table, and modify the settings in the **Auto Scaling** section\. For more information about these settings, see [Enabling DynamoDB Auto Scaling on Existing Tables](#AutoScaling.Console.ExistingTable)\.

To disable DynamoDB auto scaling, go to the **Capacity** tab for your table and clear **Read capacity**, **Write capacity**, or both\.