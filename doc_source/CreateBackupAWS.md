# Creating Backups of DynamoDB Tables with AWS Backups<a name="CreateBackupAWS"></a>

This section describes how to enable AWS Backup to create on\-demand and scheduled backups from your DynamoDB tables\. 

** [Enabling AWS Backup Features](#CreateBackupAWS_enabling) [On\-Demand Backups](#CreateBackupAWS_on-demand) [Scheduled Backups](#CreateBackupAWS_scheduled)**
+ [Enabling AWS Backup Features](#CreateBackupAWS_enabling)
+ [On\-Demand Backups](#CreateBackupAWS_on-demand)
+ [Scheduled Backups](#CreateBackupAWS_scheduled)

## Enabling AWS Backup Features<a name="CreateBackupAWS_enabling"></a>

You must enable AWS Backup to use it with DynamoDB\.

To enable AWS Backup, go through the following steps:

1. Sign in to the AWS Management Console and open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. In the navigation pane on the left side of the console, choose **Backups**\.

1. In the Backup Settings window, choose **Enable**\.

1. A confirmation screen will appear\. Choose **Enable features**\.

 AWS Backup features are now available for your DynamoDB tables\. 

If you choose to disable AWS Backup features after they’ve been enabled, follow these steps:

1. Sign in to the AWS Management Console and open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. In the navigation pane on the left side of the console, choose **Backups**\.

1. In the Backup Settings window, choose **Disable**\.

1. A confirmation screen will appear\. Choose **Disable features**\.

 If you can’t enable or disable the AWS Backup features, your AWS admin may need to perform those actions\.

## On\-Demand Backups<a name="CreateBackupAWS_on-demand"></a>

To create an on\-demand backup of a DynamoDB table, follow these steps: 

1. Sign in to the AWS Management Console and open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. In the navigation pane on the left side of the console, choose **Backups**\.

1. Choose **Create backup**\.

1. From the dropdown menu that appears, choose **Create an on\-demand backup**\.

1. To create a backup managed by AWS Backup with warm storage and other basic features, choose **Default Settings\.** To create a backup that can be transitioned to cold storage, or to create a backup with DynamoDB features instead of AWS Backup, choose **Customize settings**\.

   If you want to create this backup with previous DynamoDB features instead, choose **Customize settings** and then choose **Backup with DynamoDB**\.

1. When you have completed the settings, choose **Create backup\.**

## Scheduled Backups<a name="CreateBackupAWS_scheduled"></a>

To schedule a backup, follow these steps\. 

1. Sign in to the AWS Management Console and open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. In the navigation pane on the left side of the console, choose **Backups**\.

1. From the dropdown menu that appears, choose **Schedule backups with AWS Backup**\.

1. You will be taken to AWS Backup to create a backup plan\.