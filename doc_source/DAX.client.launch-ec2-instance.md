# Step 1: Launch an Amazon EC2 Instance<a name="DAX.client.launch-ec2-instance"></a>

When your DAX cluster is available, you can launch an Amazon EC2 instance in your default Amazon VPC\. You will then be able to install and run DAX client software on that instance\.

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch Instance** and do the following:

   ** Step 1: Choose an Amazon Machine Image \(AMI\) **
   + At the top of the list of AMIs, go to **Amazon Linux AMI** and choose **Select**\.

   **Step 2: Choose an Instance Type**
   + At the top of the list of instance types, choose **t2\.micro**\.
   + Choose **Next: Configure Instance Details**\.

   **Step 3: Configure Instance Details**
   + Go to **Network** and choose your default VPC\.
   + Choose **Next: Add Storage**\.

   **Step 4: Add Storage**
   + Skip this step by choosing **Next: Tag Instance**\.

   **Step 5: Tag Instance**
   + Skip this step by choosing **Next: Configure Security Group**\.

   **Step 6: Configure Security Group**
   + Choose **Select an existing security group**\.
   + In the list of security groups, choose **default**\. This is the default security group for your VPC\.
   + Choose **Next: Review and Launch**\.

   **Step 7: Review Instance Launch**
   + Choose **Launch**\.

1. In the **Select an existing key pair or create a new key pair** window, do one of the following:
   + If you do not have an Amazon EC2 key pair, choose **Create a new key pair** and follow the instructions\. You will be asked to download a private key file \(*\.pem* file\); you will need this file later when you log in to your Amazon EC2 instance\.
   + If you already have an existing Amazon EC2 key pair, go to ** Select a key pair** and choose your key pair from the list\. Note that you must already have the private key file \( *\.pem* file\) available in order to log in to your Amazon EC2 instance\.

1. When you have configured your key pair, choose **Launch Instances\.**

1. In the console navigation pane, choose **EC2 Dashboard** and then choose the instance that you launched\. In the lower pane, on the **Description** tab, find the **Public DNS** for your instance\. For example: `ec2-11-22-33-44.us-west-2.compute.amazonaws.com`\. Make a note of this public DNS name, because you will need it for the next step \([Step 3: Configure Your Amazon EC2 Instance](DAX.client.configure-ec2-instance.md)\)\.

**Note**  
It will take a few minutes for your Amazon EC2 instance to become available\. In the meantime, you can proceed to [Step 2: Create an IAM User and Policy](DAX.client.create-user-policy.md) and follow the instructions there\.