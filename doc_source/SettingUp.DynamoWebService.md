# Setting Up DynamoDB \(Web Service\)<a name="SettingUp.DynamoWebService"></a>

To use the Amazon DynamoDB web service:

1. [Sign up for AWS\.](#SettingUp.DynamoWebService.SignUpForAWS)

1. [Get an AWS access key](#SettingUp.DynamoWebService.GetCredentials) \(used to access DynamoDB programmatically\)\.
**Note**  
 If you plan to interact with DynamoDB only through the AWS Management Console, you don't need an AWS access key, and you can skip ahead to [Using the Console](ConsoleDynamoDB.md)\.

1. [Configure your credentials](#SettingUp.DynamoWebService.ConfigureCredentials) \(used to access DynamoDB programmatically\)\. 

## Signing Up for AWS<a name="SettingUp.DynamoWebService.SignUpForAWS"></a>

To use the DynamoDB service, you must have an AWS account\. If you don't already have an account, you are prompted to create one when you sign up\. You're not charged for any AWS services that you sign up for unless you use them\.

**To sign up for AWS**

1. Open [https://portal\.aws\.amazon\.com/billing/signup](https://portal.aws.amazon.com/billing/signup)\.

1. Follow the online instructions\.

   Part of the sign\-up procedure involves receiving a phone call and entering a verification code on the phone keypad\.

## Getting an AWS Access Key<a name="SettingUp.DynamoWebService.GetCredentials"></a>

Before you can access DynamoDB programmatically or through the AWS Command Line Interface \(AWS CLI\), you must have an AWS access key\. You don't need an access key if you plan to use the DynamoDB console only\.

Access keys consist of an access key ID and secret access key, which are used to sign programmatic requests that you make to AWS\. If you don't have access keys, you can create them from the AWS Management Console\. As a best practice, do not use the AWS account root user access keys for any task where it's not required\. Instead, [create a new administrator IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html) with access keys for yourself\.

The only time that you can view or download the secret access key is when you create the keys\. You cannot recover them later\. However, you can create new access keys at any time\. You must also have permissions to perform the required IAM actions\. For more information, see [Permissions required to access IAM resources](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_permissions-required.html) in the *IAM User Guide*\.

**To create access keys for an IAM user**

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Users**\.

1. Choose the name of the user whose access keys you want to create, and then choose the **Security credentials** tab\.

1. In the **Access keys** section, choose **Create access key**\.

1. To view the new access key pair, choose **Show**\. You will not have access to the secret access key again after this dialog box closes\. Your credentials will look something like this:
   + Access key ID: AKIAIOSFODNN7EXAMPLE
   + Secret access key: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY

1. To download the key pair, choose **Download \.csv file**\. Store the keys in a secure location\. You will not have access to the secret access key again after this dialog box closes\.

   Keep the keys confidential in order to protect your AWS account and never email them\. Do not share them outside your organization, even if an inquiry appears to come from AWS or Amazon\.com\. No one who legitimately represents Amazon will ever ask you for your secret key\.

1. After you download the `.csv` file, choose **Close**\. When you create an access key, the key pair is active by default, and you can use the pair right away\.

**Related topics**
+ [What is IAM?](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html) in the *IAM User Guide*
+ [AWS security credentials](https://docs.aws.amazon.com/general/latest/gr/aws-security-credentials.html) in *AWS General Reference* 

## Configuring Your Credentials<a name="SettingUp.DynamoWebService.ConfigureCredentials"></a>

Before you can access DynamoDB programmatically or through the AWS CLI, you must configure your credentials to enable authorization for your applications\.

 There are several ways to do this\. For example, you can manually create the credentials file to store your access key ID and secret access key\. You also can use the `aws configure` command of the AWS CLI to automatically create the file\. Alternatively, you can use environment variables\. For more information about configuring your credentials, see the programming\-specific AWS SDK developer guide\.

 To install and configure the AWS CLI, see [Using the AWS CLI](Tools.CLI.md)\. 