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

1. Open [https://aws\.amazon\.com/](https://aws.amazon.com/), and then choose **Create an AWS Account**\.
**Note**  
This might be unavailable in your browser if you previously signed into the AWS Management Console\. In that case, choose **Sign in to a different account**, and then choose **Create a new AWS account**\.

1. Follow the online instructions\.

   Part of the sign\-up procedure involves receiving a phone call and entering a PIN using the phone keypad\.

## Getting an AWS Access Key<a name="SettingUp.DynamoWebService.GetCredentials"></a>

Before you can access DynamoDB programmatically or through the AWS Command Line Interface, you must have an AWS access key\. You don't need an access key if you plan to use the DynamoDB console only\.

**To get the access key ID and secret access key for an IAM user**

Access keys consist of an access key ID and secret access key, which are used to sign programmatic requests that you make to AWS\. If you don't have access keys, you can create them from the AWS Management Console\. We recommend that you use IAM access keys instead of AWS account root user access keys\. IAM lets you securely control access to AWS services and resources in your AWS account\.

The only time that you can view or download the secret access keys is when you create the keys\. You cannot recover them later\. However, you can create new access keys at any time\. You must also have permissions to perform the required IAM actions\. For more information, see [Permissions Required to Access IAM Resources](http://docs.aws.amazon.com/IAM/latest/UserGuide/access_permissions-required.html) in the *IAM User Guide*\.

1. Open the [IAM console](https://console.aws.amazon.com/iam/home?#home)\.

1. In the navigation pane of the console, choose **Users**\.

1. Choose your IAM user name \(not the check box\)\.

1. Choose the **Security credentials** tab and then choose **Create access key**\.

1. To see the new access key, choose **Show**\. Your credentials will look something like this:
   + Access key ID: AKIAIOSFODNN7EXAMPLE
   + Secret access key: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY

1. To download the key pair, choose **Download \.csv file**\. Store the keys in a secure location\.

   Keep the keys confidential in order to protect your AWS account, and never email them\. Do not share them outside your organization, even if an inquiry appears to come from AWS or Amazon\.com\. No one who legitimately represents Amazon will ever ask you for your secret key\.

**Related topics**
+ [What Is IAM?](http://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html) in the *IAM User Guide*
+ [AWS Security Credentials](http://docs.aws.amazon.com/general/latest/gr/aws-security-credentials.html) in *AWS General Reference* 

## Configuring Your Credentials<a name="SettingUp.DynamoWebService.ConfigureCredentials"></a>

Before you can access DynamoDB programmatically or through the AWS CLI, you must configure your credentials to enable authorization for your applications\.

 There are several ways to do this\. For example, you can manually create the credentials file to store your AWS access key ID and secret access key\. You can also use the `aws configure` command of the AWS CLI to automatically create the file\. Alternatively, you can use environment variables\. For more information on configuring your credentials, see the programming\-specific AWS SDK developer guide\.

 To install and configure the AWS CLI, see [Using the CLI](Tools.CLI.md)\. 