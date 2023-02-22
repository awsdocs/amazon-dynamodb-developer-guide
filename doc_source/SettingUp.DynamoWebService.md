# Setting up DynamoDB \(web service\)<a name="SettingUp.DynamoWebService"></a>

To use the Amazon DynamoDB web service:

1. [Sign up for AWS\.](#SettingUp.DynamoWebService.SignUpForAWS)

1. [Get an AWS access key](#SettingUp.DynamoWebService.GetCredentials) \(used to access DynamoDB programmatically\)\.
**Note**  
 If you plan to interact with DynamoDB only through the AWS Management Console, you don't need an AWS access key, and you can skip ahead to [Using the console](ConsoleDynamoDB.md)\.

1. [Configure your credentials](#SettingUp.DynamoWebService.ConfigureCredentials) \(used to access DynamoDB programmatically\)\. 

## Signing up for AWS<a name="SettingUp.DynamoWebService.SignUpForAWS"></a>

To use the DynamoDB service, you must have an AWS account\. If you don't already have an account, you are prompted to create one when you sign up\. You're not charged for any AWS services that you sign up for unless you use them\.

**To sign up for AWS**

1. Open [https://portal\.aws\.amazon\.com/billing/signup](https://portal.aws.amazon.com/billing/signup)\.

1. Follow the online instructions\.

   Part of the sign\-up procedure involves receiving a phone call and entering a verification code on the phone keypad\.

   When you sign up for an AWS account, an *AWS account root user* is created\. The root user has access to all AWS services and resources in the account\. As a security best practice, [assign administrative access to an administrative user](https://docs.aws.amazon.com/singlesignon/latest/userguide/getting-started.html), and use only the root user to perform [tasks that require root user access](https://docs.aws.amazon.com/accounts/latest/reference/root-user-tasks.html)\.

## Granting programmatic access<a name="SettingUp.DynamoWebService.GetCredentials"></a>

Before you can access DynamoDB programmatically or through the AWS Command Line Interface \(AWS CLI\), you must have programmatic access\. You don't need programmatic access if you plan to use the DynamoDB console only\. 

Users need programmatic access if they want to interact with AWS outside of the AWS Management Console\. The way to grant programmatic access depends on the type of user that's accessing AWS:
+ If you manage identities in IAM Identity Center, the AWS APIs require a profile, and the AWS Command Line Interface requires a profile or an environment variable\.
+ If you have IAM users, the AWS APIs and the AWS Command Line Interface require access keys\. Whenever possible, create temporary credentials that consist of an access key ID, a secret access key, and a security token that indicates when the credentials expire\.

To grant users programmatic access, choose one of the following options\.


****  

| Which user needs programmatic access? | To | By | 
| --- | --- | --- | 
|  Workforce identity \(Users managed in IAM Identity Center\)  | Use short\-term credentials to sign programmatic requests to the AWS CLI or AWS APIs \(directly or by using the AWS SDKs\)\. |  Following the instructions for the interface that you want to use: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/SettingUp.DynamoWebService.html)  | 
| IAM | Use short\-term credentials to sign programmatic requests to the AWS CLI or AWS APIs \(directly or by using the AWS SDKs\)\. | Following the instructions in [Using temporary credentials with AWS resources](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_use-resources.html) in the IAM User Guide\. | 
| IAM | Use long\-term credentials to sign programmatic requests to the AWS CLI or AWS APIs \(directly or by using the AWS SDKs\)\.\(Not recommended\) | Following the instructions in [Managing access keys for IAM users](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html) in the IAM User Guide\. | 

## Configuring your credentials<a name="SettingUp.DynamoWebService.ConfigureCredentials"></a>

Before you can access DynamoDB programmatically or through the AWS CLI, you must configure your credentials to enable authorization for your applications\.

 There are several ways to do this\. For example, you can manually create the credentials file to store your access key ID and secret access key\. You also can use the `aws configure` command of the AWS CLI to automatically create the file\. Alternatively, you can use environment variables\. For more information about configuring your credentials, see the programming\-specific AWS SDK developer guide\.

 To install and configure the AWS CLI, see [Using the AWS CLI](Tools.CLI.md)\. 