# Step 3: Configure Your Amazon EC2 Instance<a name="DAX.client.configure-ec2-instance"></a>

When your Amazon EC2 instance is available, you can log into it and prepare it for use\.

**Note**  
The following steps assume that you are connecting to your Amazon EC2 instance from a computer running Linux\. For other ways to connect, see [Connect to Your Linux Instance](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstances.html) in the Amazon EC2 User Guide for Linux Instances\.

1. If you haven't already done so, open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Use the `ssh` command to log in to your Amazon EC2 instance\. For example:

   ```
   ssh -i my-keypair.pem ec2-user@public-dns-name
   ```

   You will need to specify your private key file \(*\.pem* file\) and the public DNS name of your instance\. \(See [Step 1: Launch an Amazon EC2 Instance](DAX.client.launch-ec2-instance.md)\)\. 

   The login ID is `ec2-user`\. No password is required\.

1. After you have logged in to your EC2 instance, you will need to configure your AWS credentials as shown below\. Enter your AWS access key ID and secret key \(from [Step 2: Create an IAM User and Policy](DAX.client.create-user-policy.md)\), and set the default region name to your current region\. \(In the following example, the default region name is `us-west-2`\.\)

   ```
   aws configure
   
   AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE
   AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
   Default region name [None]: us-west-2
   Default output format [None]:
   ```