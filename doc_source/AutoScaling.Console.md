# Using the AWS Management Console With DynamoDB Auto Scaling<a name="AutoScaling.Console"></a>


+ [Before You Begin: Grant User Permissions for DynamoDB Auto Scaling](#AutoScaling.Permissions)
+ [Creating a New Table with Auto Scaling Enabled](#AutoScaling.Console.NewTable)
+ [Enabling DynamoDB Auto Scaling on Existing Tables](#AutoScaling.Console.ExistingTable)
+ [Viewing Auto Scaling Activities in the Console](#AutoScaling.Console.ViewingActivities)
+ [Modifying or Disabling DynamoDB Auto Scaling Settings](#AutoScaling.Console.Modifying)

When you use the AWS Management Console to create a new table, DynamoDB auto scaling is enabled for that table by default\. You can also use the console to enable auto scaling for existing tables, modify auto scaling settings, or disable auto scaling\.

## Before You Begin: Grant User Permissions for DynamoDB Auto Scaling<a name="AutoScaling.Permissions"></a>

In AWS Identity and Access Management \(IAM\), the AWS\-managed policy `DynamoDBFullAccess` provides the required permissions for using the DynamoDB console\. However, for DynamoDB auto scaling, IAM users will require some additional privileges\. 

**Important**  
 `application-autoscaling:*` permissions are required to delete an autoscaling enabled table\. The AWS\-managed policy `DynamoDBFullAccess` attached next includes such permissions\. 

To set up an IAM user for DynamoDB console access and DynamoDB auto scaling, add the following two policies:

**To attach the *AmazonDynamoDBFullAccess* policy**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. On the IAM console dashboard, choose **Users**, and then choose your IAM user from the list\.

1. On the **Summary** page, choose **Add permissions**\.

1. Choose **Attach existing policies directly**\.

1. From the list of policies, choose **AmazonDynamoDBFullAccess**, and then choose **Next: Review**\.

1. Choose **Add permissions**\.

**To add a custom inline policy**

1. On the IAM console dashboard, choose **Users**, and then choose your IAM user from the list\.

1. On the **Summary** page, choose **Add inline policy**\.

1. On the **Set Permissions** page, choose **Custom Policy**, and then choose **Select**\.

1. On the **Review Policy** page, do the following:

   1. **Policy Name**—type a name for the policy\. For example: *MyDynamoDBAutoscalingPolicy*

   1. **Policy Document**—copy and paste the following:

      ```
      {
          "Version": "2012-10-17",
          "Statement": [
              {
                  "Effect": "Allow",
                  "Action": [
                      "iam:AttachRolePolicy",
                      "iam:CreatePolicy",
                      "iam:CreateRole"
                  ],
                  "Resource": "*"
              }
          ]
      }
      ```

1. After you have done this, choose **Apply Policy**\.

## Creating a New Table with Auto Scaling Enabled<a name="AutoScaling.Console.NewTable"></a>

**Note**  
DynamoDB auto scaling requires the presence of a role \(*DynamoDBAutoscaleRole*\) that performs auto scaling actions on your behalf\. You do not need to create this role yourself\. Instead, the DynamoDB console creates the role automatically, when you create a new table with auto scaling for the first time\.

**To create a new table with auto scaling enabled**

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. Choose **Create Table**\. 

1. On the **Create DynamoDB table** page, enter a **Table name** and **Primary key** details\.

1. If you see the following message, then your AWS account does not currently have the *DynamoDBAutoscaleRole*:  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

   In this case, do the following:

   1. Deselect **Use default settings**\.

   1. In the **Auto scaling** section, ensure that **New role: DynamoDBAutoscaleRole** is selected\.

   Otherwise, simply ensure that **Use default settings** is selected\. \(Your AWS account already has *DynamoDBAutoscaleRole*\.\)

1. When the settings are as you want them, choose **Create**\. Your table will be created with default auto scaling parameters\.

## Enabling DynamoDB Auto Scaling on Existing Tables<a name="AutoScaling.Console.ExistingTable"></a>

**Note**  
DynamoDB auto scaling requires the presence of a role \(*DynamoDBAutoscaleRole*\) that performs auto scaling actions on your behalf\. The DynamoDB console will create the role for you when you create a new table with auto scaling for the first time\.  
If you have never used DynamoDB auto scaling before, see [Creating a New Table with Auto Scaling Enabled](#AutoScaling.Console.NewTable)\. The DynamoDB console creates *DynamoDBAutoscaleRole* automatically, when you create a new table with auto scaling for the first time\.

To enable DynamoDB auto scaling for an existing table, do the following:

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. Choose the table that you want to work with, and then choose **Capacity**\.

1. In the **Auto Scaling** section, do the following:

   1. Select **Read capacity**, **Write capacity**, or both\. \(For write capacity, note that you can choose **Same settings as read**\.\) For each of these, do the following:

     + **Target utilization**—Type your target utilization percentage for the table\.

     + **Minimum provisioned capacity**—Type your lower boundary for the auto scaling range\.

     + **Maximum provisioned capacity**—Type your upper boundary for the auto scaling range\.

     + **Apply same settings to global secondary indexes**—Leave this option at its default setting \(enabled\)\. 
**Note**  
For best performance, we recommend that you enable **Apply same settings to global secondary indexes**\. This option allows DynamoDB auto scaling to uniformly scale all the global secondary indexes on the base table\. This includes existing global secondary indexes, and any others that you create for this table in the future\.  
With this option enabled, you can't set a scaling policy on an individual global secondary index\.

     \(For **Write capacity**, note that you can choose **Same settings as read**\.\)

     In the **IAM Role** section, ensure that **Existing role with pre\-defined policies** is selected, and that **Role Name** is set to **DynamoDBAutoscaleRole**\.

1. When the settings are as you want them, choose **Save**\.

## Viewing Auto Scaling Activities in the Console<a name="AutoScaling.Console.ViewingActivities"></a>

As your application drives read and write traffic to your table, DynamoDB auto scaling dynamically modifies the table's throughput settings\.

To view these auto scaling activities in the DynamoDB console, choose the table that you want to work with\. Choose **Capacity**, and then expand the **Scaling activities** section\. When your table's throughput settings are modified, you will see informational messages here\.

## Modifying or Disabling DynamoDB Auto Scaling Settings<a name="AutoScaling.Console.Modifying"></a>

You can use the AWS Management Console to modify your DynamoDB auto scaling settings\. To do this, go to the **Capacity** tab for your table and modify the settings in the **Auto Scaling** section\. For more information about these settings, see [Enabling DynamoDB Auto Scaling on Existing Tables](#AutoScaling.Console.ExistingTable)\.

To disable DynamoDB auto scaling, go to the **Capacity** tab for your table and clear **Read capacity**, **Write capacity**, or both\.