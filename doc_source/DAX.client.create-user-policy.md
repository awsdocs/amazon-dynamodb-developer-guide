# Step 2: Create an IAM User and Policy<a name="DAX.client.create-user-policy"></a>

In this step, you will create an IAM user with a policy that grants access to your DAX cluster and to DynamoDB\. You will then be able to run applications that interact with your DAX cluster\.

****

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Users**\.

1. Choose **Add user**\.

1. On the **Details** page, enter the following information:

   + **User name**—type a unique name\. For example: `MyDAXUser`

   + **Access type**—choose **Programmatic access**\.

   Choose **Next: Permissions**\.

1. On the **Permissions** page, choose **Attach existing policies directly**, and then choose **Create policy**\.

1. On the **Create Policy** page, select **Create Your Own Policy**\.

1. On the Review Policy page, enter the following information:

   + **Policy Name**—type a unique name\. For example: `MyDAXUserPolicy`\.

   + **Description**—type a short description for the policy

   + **Policy Document**—copy and paste the following document:

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

   Choose **Create Policy**\.

1. Return to the **Permissions** page\. In the list of policies, choose **Refresh**\. To narrow the list of policies, choose **Filter | Customer managed**\. Choose the IAM policy you created in the previous step \(for example: `MyDAXUserPolicy`\), and then choose **Next: Review**\.

1. On the **Review** page, choose **Create user**\.

1. On the **Complete** page, go to the **Secret access key** and choose **Show**\. After you do this, copy both the **Access key ID** and **Secret access key**\. You will need both of these identifiers for [Step 3: Configure Your Amazon EC2 Instance](DAX.client.configure-ec2-instance.md)\.