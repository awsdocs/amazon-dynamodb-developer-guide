# Step 1: Launch an Amazon EC2 instance<a name="DAX.client.launch-ec2-instance"></a>

When your Amazon DynamoDB Accelerator \(DAX\) cluster is available, you can launch an Amazon EC2 instance in your default Amazon VPC\. You can then install and run DAX client software on that instance\.

**To launch an EC2 instance**

1. Sign in to the AWS Management Console and open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch Instance**, and do the following:

   ** Step 1: Choose an Amazon Machine Image \(AMI\)**

   1. In the list of AMIs, find the **Amazon Linux AMI**, and choose **Select**\.

   **Step 2: Choose an Instance Type**

   1. In the list of instance types, choose **t2\.micro**\.

   1. Choose **Next: Configure Instance Details**\.

   **Step 3: Configure Instance Details**

   1. For **Network**, choose your default VPC\.

   1. Choose **Next: Add Storage**\.

   **Step 4: Add Storage**

   1. Skip this step by choosing **Next: Add Tags**\.

   **Step 5: Add Tags**

   1. Skip this step by choosing **Next: Configure Security Group**\.

   **Step 6: Configure Security Group**

   1. Choose **Select an existing security group**\.

   1. In the list of security groups, choose **default**\. This is the default security group for your VPC\.

   1. Choose **Next: Review and Launch**\.

   **Step 7: Review Instance Launch**

   1. Choose **Launch**\.

1. In the **Select an existing key pair or create a new key pair** window, do one of the following:
   + If you don't have an Amazon EC2 key pair, choose **Create a new key pair** and follow the instructions\. You are asked to download a private key file \(`.pem` file\)\. You need this file later when you log in to your Amazon EC2 instance\.
   + If you already have an existing Amazon EC2 key pair, go to ** Select a key pair** and choose your key pair from the list\. You must already have the private key file \(`.pem` file\) available in order to log in to your Amazon EC2 instance\.

1. After configuring your key pair, choose **Launch Instances**\.

1. In the console navigation pane, choose **EC2 Dashboard**, and then choose the instance that you launched\. In the lower pane, on the **Description** tab, find the **Public DNS** for your instance, for example: `ec2-11-22-33-44.us-west-2.compute.amazonaws.com`\. Make a note of this public DNS name because you need it for [Step 3: Configure an Amazon EC2 instance](DAX.client.configure-ec2-instance.md)\.

**Note**  
It takes a few minutes for your Amazon EC2 instance to become available\. In the meantime, proceed to [Step 2: Create a user and policy](DAX.client.create-user-policy.md) and follow the instructions there\.