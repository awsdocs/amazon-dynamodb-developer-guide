# Step 2: Create an IAM User and Policy<a name="DAX.client.create-user-policy"></a>

In this step, you create an AWS Identity and Access Management \(IAM\) user with a policy that grants access to your Amazon DynamoDB Accelerator \(DAX\) cluster and to DynamoDB\. You can then run applications that interact with your DAX cluster\.

**To create an IAM user and policy**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Users**\.

1. Choose **Add user**\.

1. On the **Details** page, enter the following information:
   + **User name**—Enter a unique name, for example: `MyDAXUser`\.
   + **Access type**—Choose **Programmatic access**\.

   Choose **Next: Permissions**\.

1. On the **Set permissions** page, choose **Attach existing policies directly**, and then choose **Create policy**\.

1. On the **Create policy** page, choose **Create Your Own Policy**\.

1. On the **Review policy** page, provide the following information:
   + **Policy Name**—Enter a unique name, for example, `MyDAXUserPolicy`\.
   + **Description**—Enter a short description for the policy\.
   + **Policy Document**—Copy and paste the following document\.

     ```
     {
         "Version": "2012-10-17",
         "Statement": [
             {
                 "Action": [
                     "dax:*"
                 ],
                 "Effect": "Allow",
                 "Resource": [
                     "*"
                 ]
             },
             {
                 "Action": [
                     "dynamodb:*"
                 ],
                 "Effect": "Allow",
                 "Resource": [
                     "*"
                 ]
             }
         ]
     }
     ```

   Choose **Create policy**\.

1. Return to the **Permissions** page\. In the list of policies, choose **Refresh**\.

1. To narrow the list of policies, choose **Filter**, **Customer managed**\. Choose the IAM policy that you created in the previous step \(for example: `MyDAXUserPolicy`\), and then choose **Next: Review**\.

1. On the **Review** page, choose **Create user**\.

1. On the **Complete** page, go to the **Secret access key** and choose **Show**\. After you do this, copy both the **Access key ID** and **Secret access key**\. You need both of these identifiers for [Step 3: Configure an Amazon EC2 Instance](DAX.client.configure-ec2-instance.md)\.