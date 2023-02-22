# Step 3: Configure an Amazon EC2 instance<a name="DAX.client.configure-ec2-instance"></a>

When your Amazon EC2 instance is available, you can log in to the instance and prepare it for use\.

**Note**  
The following steps assume that you are connecting to your Amazon EC2 instance from a computer running Linux\. For other ways to connect, see [Connect to Your Linux Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstances.html) in the *Amazon EC2 User Guide for Linux Instances*\.

**To configure the EC2 instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Use the `ssh` command to log in to your Amazon EC2 instance, as shown in the following example\.

   ```
   ssh -i my-keypair.pem ec2-user@public-dns-name
   ```

   You need to specify your private key file \(`.pem` file\) and the public DNS name of your instance\. \(See [Step 1: Launch an Amazon EC2 instance](DAX.client.launch-ec2-instance.md)\.\)

   The login ID is `ec2-user`\. No password is required\.

1. After you log in to your EC2 instance, configure your AWS credentials as shown following\. Enter your AWS access key ID and secret key \(from [Step 2: Create a user and policy](DAX.client.create-user-policy.md)\), and set the default Region name to your current Region\. \(In the following example, the default Region name is `us-west-2`\.\)

   ```
   aws configure
   
   AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE
   AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
   Default region name [None]: us-west-2
   Default output format [None]:
   ```

After launching and configuring your Amazon EC2 instance, you can test the functionality of DAX using one of the available sample applications\. For more information, see [Step 4: Run a sample application](DAX.client.run-application.md)\. 