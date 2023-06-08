# Exporting and importing DynamoDB data using AWS Data Pipeline<a name="DynamoDBPipeline"></a>

You can use AWS Data Pipeline to export data from a DynamoDB table to a file in an Amazon S3 bucket\. You can also use the console to import data from Amazon S3 into a DynamoDB table, in the same AWS region or in a different region\.

**Note**  
 DynamoDB Console now natively supports importing from Amazon S3 and exporting to Amazon S3\. These flows are not compatible with AWS Data Pipeline import flow\. For more information see [Import from Amazon S3](S3DataImport.HowItWorks.md), [Export from Amazon S3](S3DataExport.HowItWorks.md), and the blog post [ Export Amazon DynamoDB table data to your data lake in Amazon S3](https://aws.amazon.com/blogs/aws/new-export-amazon-dynamodb-table-data-to-data-lake-amazon-s3)\. 

The ability to export and import data is useful in many scenarios\. For example, suppose you want to maintain a baseline set of data, for testing purposes\. You could put the baseline data into a DynamoDB table and export it to Amazon S3\. Then, after you run an application that modifies the test data, you could "reset" the data set by importing the baseline from Amazon S3 back into the DynamoDB table\. Another example involves accidental deletion of data, or even an accidental `DeleteTable` operation\. In these cases, you could restore the data from a previous export file in Amazon S3\. You can even copy data from a DynamoDB table in one AWS region, store the data in Amazon S3, and then import the data from Amazon S3 to an identical DynamoDB table in a second region\. Applications in the second region could then access their nearest DynamoDB endpoint and work with their own copy of the data, with reduced network latency\.

**Important**  
DynamoDB Backup and Restore is a fully managed feature\. You can back up tables from a few megabytes to hundreds of terabytes of data, with no impact on the performance and availability of your production applications\. You can restore your table with a single click in the AWS Management Console or a single API call\. We highly recommend that you use DynamoDB's native backup and restore feature instead of using AWS Data Pipeline\. For more information, see [Using On\-Demand backup and restore for DynamoDB](BackupRestore.md)\.

The following diagram shows an overview of exporting and importing DynamoDB data using AWS Data Pipeline\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/ddb-awsdp-exportimport-overview.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

To export a DynamoDB table, you use the AWS Data Pipeline console to create a new pipeline\. The pipeline launches an Amazon EMR cluster to perform the actual export\. Amazon EMR reads the data from DynamoDB, and writes the data to an export file in an Amazon S3 bucket\.

The process is similar for an import, except that the data is read from the Amazon S3 bucket and written to the DynamoDB table\.

**Important**  
When you export or import DynamoDB data, you will incur additional costs for the underlying AWS services that are used:  
**AWS Data Pipeline**— manages the import/export workflow for you\.
**Amazon S3**— contains the data that you export from DynamoDB, or import into DynamoDB\.
**Amazon EMR**— runs a managed Hadoop cluster to perform reads and writes between DynamoDB to Amazon S3\. The cluster configuration is one `m3.xlarge` instance leader node and one `m3.xlarge` instance core node\.
For more information see [AWS Data Pipeline pricing](https://aws.amazon.com/datapipeline/pricing), [Amazon EMR pricing](https://aws.amazon.com/elasticmapreduce/pricing), and [Amazon S3 pricing](https://aws.amazon.com/s3/pricing)\.

## Prerequisites to export and import data<a name="DataPipelineExportImport.Prereqs"></a>

When you use AWS Data Pipeline for exporting and importing data, you must specify the actions that the pipeline is allowed to perform, and which resources the pipeline can consume\. The permitted actions and resources are defined using AWS Identity and Access Management \(IAM\) roles\.

You can also control access by creating IAM policies and attaching them to users, roles or groups\. These policies let you specify which users are allowed to import and export your DynamoDB data\.

**Important**  
Users need programmatic access if they want to interact with AWS outside of the AWS Management Console\. The way to grant programmatic access depends on the type of user that's accessing AWS:  
If you manage identities in IAM Identity Center, the AWS APIs require a profile, and the AWS Command Line Interface requires a profile or an environment variable\.
If you have IAM users, the AWS APIs and the AWS Command Line Interface require access keys\. Whenever possible, create temporary credentials that consist of an access key ID, a secret access key, and a security token that indicates when the credentials expire\.
To grant users programmatic access, choose one of the following options\.  


****  

| Which user needs programmatic access? | To | By | 
| --- | --- | --- | 
|  Workforce identity \(Users managed in IAM Identity Center\)  | Use short\-term credentials to sign programmatic requests to the AWS CLI or AWS APIs \(directly or by using the AWS SDKs\)\. |  Following the instructions for the interface that you want to use: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DynamoDBPipeline.html)  | 
| IAM | Use short\-term credentials to sign programmatic requests to the AWS CLI or AWS APIs \(directly or by using the AWS SDKs\)\. | Following the instructions in [Using temporary credentials with AWS resources](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_use-resources.html) in the IAM User Guide\. | 
| IAM | Use long\-term credentials to sign programmatic requests to the AWS CLI or AWS APIs \(directly or by using the AWS SDKs\)\.\(Not recommended\) | Following the instructions in [Managing access keys for IAM users](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html) in the IAM User Guide\. | 
 

### Creating IAM roles for AWS Data Pipeline<a name="DataPipelineExportImport.Prereqs.IAMRoles"></a>

In order to use AWS Data Pipeline, the following IAM roles must be present in your AWS account:
+ ***DataPipelineDefaultRole*** — the actions that your pipeline can take on your behalf\.
+ ***DataPipelineDefaultResourceRole*** — the AWS resources that the pipeline will provision on your behalf\. For exporting and importing DynamoDB data, these resources include an Amazon EMR cluster and the Amazon EC2 instances associated with that cluster\.

If you have never used AWS Data Pipeline before, you will need to create *DataPipelineDefaultRole* and *DataPipelineDefaultResourceRole* yourself\. Once you have created these roles, you can use them any time you want to export or import DynamoDB data\.

**Note**  
If you have previously used the AWS Data Pipeline console to create a pipeline, then *DataPipelineDefaultRole* and *DataPipelineDefaultResourceRole* were created for you at that time\. No further action is required; you can skip this section and begin creating pipelines using the DynamoDB console\. For more information, see [Exporting data from DynamoDB to Amazon S3](#DataPipelineExportImport.Exporting) and [Importing data from Amazon S3 to DynamoDB](#DataPipelineExportImport.Importing)\.

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1.  From the IAM Console Dashboard, click **Roles**\.

1. Click **Create Role** and do the following:

   1. In the **AWS Service** trusted entity, choose **Data Pipeline**\.

   1. In the **Select your use case** panel, choose **Data Pipeline** and then choose **Next:Permissions**\.

   1. Notice that the `AWSDataPipelineRole` policy is automatically attached\. Choose **Next:Review**\.

   1. In the **Role name** field, type `DataPipelineDefaultRole` as the role name and choose **Create role**\.

1. Click **Create Role** and do the following:

   1. In the **AWS Service** trusted entity, choose **Data Pipeline**\.

   1. In the **Select your use case** panel, choose **EC2 Role for Data Pipeline** and then choose **Next:Permissions**\.

   1. Notice that the `AmazonEC2RoleForDataPipelineRole` policy is automatically attached\. Choose **Next:Review**\.

   1. In the **Role name** field, type `DataPipelineDefaultResourceRole` as the role name and choose **Create role**\.

Now that you have created these roles, you can begin creating pipelines using the DynamoDB console\. For more information, see [Exporting data from DynamoDB to Amazon S3](#DataPipelineExportImport.Exporting) and [Importing data from Amazon S3 to DynamoDB](#DataPipelineExportImport.Importing)\.

### Granting users and groups permission to perform export and import tasks using AWS Identity and Access Management<a name="DataPipelineExportImport.Prereqs.PrivsForIAMUsers"></a>

If you want to allow other users, roles or groups to export and import your DynamoDB table data, you can create an IAM policy and attach it to the users or groups that you designate\. The policy contains only the necessary permissions for performing these tasks\.

#### Granting full access<a name="DataPipelineExportImport.Prereqs.PrivsForIAMUsers.PolicyAllDDBResources"></a>

The following procedure describes how to attach the AWS managed policies `AmazonDynamoDBFullAccess`,`AWSDataPipeline_FullAccess` and an Amazon EMR inline policy to a user\. These managed policies provides full access to AWS Data Pipeline and to DynamoDB resources, and used with the Amazon EMR inline policy, allow the user to perform the actions described in this documentation\.

**Note**  
To limit the scope of the suggested permissions, the inline policy above is enforcing the usage of the tag `dynamodbdatapipeline`\. If you want to utilize this documentation without this limitation, you can remove the `Condition` section of the suggested policy\. 

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. From the IAM Console Dashboard, click **Users** and select the user you want to modify\.

1. In the **Permissions** tab, click **Add Policy**\.

1. In the **Attach permissions** panel, click **Attach existing policies directly**\.

1. Select both `AmazonDynamoDBFullAccess` and `AWSDataPipeline_FullAccess` and click **Next:Review**\.

1. Click **Add permissions**\.

1. Back on **Permissions** tab, click **Add inline policy**\.

1. In the **Create a policy** page, click **JSON** tab\.

1. Paste the content below\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Sid": "EMR",
               "Effect": "Allow",
               "Action": [
                   "elasticmapreduce:DescribeStep",
                   "elasticmapreduce:DescribeCluster",
                   "elasticmapreduce:RunJobFlow",
                   "elasticmapreduce:TerminateJobFlows"
               ],
               "Resource": "*",
               "Condition": {
                   "Null": {
                       "elasticmapreduce:RequestTag/dynamodbdatapipeline": "false"
                   }
               }
           }
       ]
   }
   ```

1. Click **Review policy**\.

1. Type `EMRforDynamoDBDataPipeline` on the name field\.

1. Click **Create policy**\.

**Note**  
You can use a similar procedure to attach this managed policy to a role or group, rather than to a user\.

#### Restricting access to particular DynamoDB tables<a name="DataPipelineExportImport.Prereqs.PrivsForIAMUsers.RestrictingAccess"></a>

If you want to restrict access so that a user can only export or import a subset of your tables, you will need to create a customized IAM policy document\. You can use the process described on [Granting full access](#DataPipelineExportImport.Prereqs.PrivsForIAMUsers.PolicyAllDDBResources) as a starting point for your custom policy, and then modify the policy so that a user can only work with the tables that you specify\.

For example, suppose that you want to allow a user to export and import only the *Forum*, *Thread*, and *Reply* tables\. This procedure describes how to create a custom policy so that a user can work with those tables, but no others\.

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. From the IAM Console Dashboard, click **Policies** and then click **Create Policy**\.

1. In the **Create Policy** panel, go to **Copy an AWS Managed Policy** and click **Select**\.

1. In the **Copy an AWS Managed Policy** panel, go to `AmazonDynamoDBFullAccess` and click **Select**\.

1. In the **Review Policy** panel, do the following:

   1. Review the autogenerated **Policy Name** and **Description**\. If you want, you can modify these values\.

   1. In the **Policy Document** text box, edit the policy to restrict access to specific tables\. By default, the policy permits all DynamoDB actions on all of your tables:

      ```
      {
          "Version": "2012-10-17",
          "Statement": [
              {
                  "Action": [
                      "cloudwatch:DeleteAlarms",
                      "cloudwatch:DescribeAlarmHistory",
                      "cloudwatch:DescribeAlarms",
                      "cloudwatch:DescribeAlarmsForMetric",
                      "cloudwatch:GetMetricStatistics",
                      "cloudwatch:ListMetrics",
                      "cloudwatch:PutMetricAlarm",
                      "dynamodb:*",
                      "sns:CreateTopic",
                      "sns:DeleteTopic",
                      "sns:ListSubscriptions",
                      "sns:ListSubscriptionsByTopic",
                      "sns:ListTopics",
                      "sns:Subscribe",
                      "sns:Unsubscribe"
                  ],
                  "Effect": "Allow",
                  "Resource": "*",
                  "Sid": "DDBConsole"
              },
      
      ...remainder of document omitted...
      ```

      To restrict the policy, first remove the following line:

      ```
                      "dynamodb:*",
      ```

      Next, construct a new `Action` that allows access to only the *Forum*, *Thread* and *Reply* tables:

      ```
      {
          "Action": [
              "dynamodb:*"
          ],
          "Effect": "Allow",
          "Resource": [
              "arn:aws:dynamodb:us-west-2:123456789012:table/Forum",
              "arn:aws:dynamodb:us-west-2:123456789012:table/Thread",
              "arn:aws:dynamodb:us-west-2:123456789012:table/Reply"
          ]
      },
      ```
**Note**  
Replace `us-west-2` with the region in which your DynamoDB tables reside\. Replace `123456789012` with your AWS account number\.

      Finally, add the new `Action` to the policy document:

      ```
      {
          "Version": "2012-10-17",
          "Statement": [
              {
                  "Action": [
                      "dynamodb:*"
                  ],
                  "Effect": "Allow",
                  "Resource": [
                      "arn:aws:dynamodb:us-west-2:123456789012:table/Forum",
                      "arn:aws:dynamodb:us-west-2:123456789012:table/Thread",
                      "arn:aws:dynamodb:us-west-2:123456789012:table/Reply"
                  ]
              },
              {
                  "Action": [
                      "cloudwatch:DeleteAlarms",
                      "cloudwatch:DescribeAlarmHistory",
                      "cloudwatch:DescribeAlarms",
                      "cloudwatch:DescribeAlarmsForMetric",
                      "cloudwatch:GetMetricStatistics",
                      "cloudwatch:ListMetrics",
                      "cloudwatch:PutMetricAlarm",
                      "sns:CreateTopic",
                      "sns:DeleteTopic",
                      "sns:ListSubscriptions",
                      "sns:ListSubscriptionsByTopic",
                      "sns:ListTopics",
                      "sns:Subscribe",
                      "sns:Unsubscribe"
                  ],
                  "Effect": "Allow",
                  "Resource": "*",
                  "Sid": "DDBConsole"
              },
      
      ...remainder of document omitted...
      ```

1. When the policy settings are as you want them, click **Create Policy**\.

After you have created the policy, you can attach it to a user\.

1. From the IAM Console Dashboard, click **Users** and select the user you want to modify\.

1. In the **Permissions** tab, click **Attach Policy**\.

1. In the **Attach Policy** panel, select the name of your policy and click **Attach Policy**\.

**Note**  
You can use a similar procedure to attach your policy to a role or group, rather than to a user\.

## Exporting data from DynamoDB to Amazon S3<a name="DataPipelineExportImport.Exporting"></a>

This section describes how to export data from one or more DynamoDB tables to an Amazon S3 bucket\. You need to create the Amazon S3 bucket before you can perform the export\.

**Important**  
If you have never used AWS Data Pipeline before, you will need to set up two IAM roles before following this procedure\. For more information, see [Creating IAM roles for AWS Data Pipeline](#DataPipelineExportImport.Prereqs.IAMRoles)\.

1. Sign in to the AWS Management Console and open the AWS Data Pipeline console at [https://console\.aws\.amazon\.com/datapipeline/](https://console.aws.amazon.com/datapipeline/)\.

1. If you do not already have any pipelines in the current AWS region, choose **Get started now**\. 

   Otherwise, if you already have at least one pipeline, choose **Create new pipeline**\.

1. On the **Create Pipeline** page, do the following:

   1. In the **Name** field, type a name for your pipeline\. For example: `MyDynamoDBExportPipeline`\.

   1. For the **Source** parameter, select **Build using a template**\. From the drop\-down template list, choose **Export DynamoDB table to S3**\.

   1. In the **Source DynamoDB table name** field, type the name of the DynamoDB table that you want to export\.

   1. In the **Output S3 Folder** text box, enter an Amazon S3 URI where the export file will be written\. For example: `s3://mybucket/exports`

      The format of this URI is `s3://bucketname/folder` where:
      + `bucketname` is the name of your Amazon S3 bucket\.
      + `folder` is the name of a folder within that bucket\. If the folder does not exist, it will be created automatically\. If you do not specify a name for the folder, a name will be assigned for it in the form `s3://bucketname/region/tablename`\.

   1. In the **S3 location for logs** text box, enter an Amazon S3 URI where the log file for the export will be written\. For example: `s3://mybucket/logs/`

      The URI format for** S3 Log Folder** is the same as for **Output S3 Folder**\. The URI must resolve to a folder; log files cannot be written to the top level of the S3 bucket\.

1. Add a tag with the Key `dynamodbdatapipeline` and the Value `true`\.

1. When the settings are as you want them, click **Activate**\.

Your pipeline will now be created; this process can take several minutes to complete\. You can monitor the progress in the AWS Data Pipeline console\.

When the export has finished, you can go to the [Amazon S3 console](https://console.aws.amazon.com/s3) to view your export file\. The output file name is an identifier value with no extension, such as this example: `ae10f955-fb2f-4790-9b11-fbfea01a871e_000000`\. The internal format of this file is described at [Verify data export file](https://docs.aws.amazon.com/datapipeline/latest/DeveloperGuide/dp-importexport-ddb-pipelinejson-verifydata2.html) in the *AWS Data Pipeline Developer Guide*\.

## Importing data from Amazon S3 to DynamoDB<a name="DataPipelineExportImport.Importing"></a>

This section assumes that you have already exported data from a DynamoDB table, and that the export file has been written to your Amazon S3 bucket\. The internal format of this file is described at [Verify data export file](https://docs.aws.amazon.com/datapipeline/latest/DeveloperGuide/dp-importexport-ddb-pipelinejson-verifydata2.html) in the *AWS Data Pipeline Developer Guide*\. Note that this is the *only* file format that DynamoDB can import using AWS Data Pipeline\.

We will use the term *source table* for the original table from which the data was exported, and *destination table* for the table that will receive the imported data\. You can import data from an export file in Amazon S3, provided that all of the following are true:
+ The destination table already exists\. \(The import process will not create the table for you\.\)
+ The destination table has the same key schema as the source table\.

The destination table does not have to be empty\. However, the import process will replace any data items in the table that have the same keys as the items in the export file\. For example, suppose you have a *Customer* table with a key of *CustomerId*, and that there are only three items in the table \(*CustomerId* 1, 2, and 3\)\. If your export file also contains data items for *CustomerID* 1, 2, and 3, the items in the destination table will be replaced with those from the export file\. If the export file also contains a data item for *CustomerId* 4, then that item will be added to the table\.

The destination table can be in a different AWS region\. For example, suppose you have a *Customer* table in the US West \(Oregon\) region and export its data to Amazon S3\. You could then import that data into an identical *Customer* table in the Europe \(Ireland\) region\. This is referred to as a *cross\-region* export and import\. For a list of AWS regions, go to [Regions and endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html) in the *AWS General Reference*\.

Note that the AWS Management Console lets you export multiple source tables at once\. However, you can only import one table at a time\.

1. Sign in to the AWS Management Console and open the AWS Data Pipeline console at [https://console\.aws\.amazon\.com/datapipeline/](https://console.aws.amazon.com/datapipeline/)\.

1. \(Optional\) If you want to perform a cross region import, go to the upper right corner of the window and choose the destination region\.

1. Choose **Create new pipeline**\.

1. On the **Create Pipeline** page, do the following:

   1. In the **Name** field, type a name for your pipeline\. For example: `MyDynamoDBImportPipeline`\.

   1. For the **Source** parameter, select **Build using a template**\. From the drop\-down template list, choose **Import DynamoDB backup data from S3**\.

   1. In the **Input S3 Folder** text box, enter an Amazon S3 URI where the export file can be found\. For example: `s3://mybucket/exports`

      The format of this URI is `s3://bucketname/folder` where:
      + `bucketname` is the name of your Amazon S3 bucket\.
      + `folder` is the name of the folder that contains the export file\.

      The import job will expect to find a file at the specified Amazon S3 location\. The internal format of the file is described at [Verify data export file](https://docs.aws.amazon.com/datapipeline/latest/DeveloperGuide/dp-importexport-ddb-pipelinejson-verifydata2.html) in the *AWS Data Pipeline Developer Guide*\.

   1. In the **Target DynamoDB table name** field, type the name of the DynamoDB table into which you want to import the data\.

   1. In the **S3 location for logs** text box, enter an Amazon S3 URI where the log file for the import will be written\. For example: `s3://mybucket/logs/`

      The URI format for** S3 Log Folder** is the same as for **Output S3 Folder**\. The URI must resolve to a folder; log files cannot be written to the top level of the S3 bucket\.

   1. Add a tag with the Key `dynamodbdatapipeline` and the Value `true`\.

1. When the settings are as you want them, click **Activate**\.

Your pipeline will now be created; this process can take several minutes to complete\. The import job will begin immediately after the pipeline has been created\.

## Troubleshooting<a name="DataPipelineExportImport.Troubleshooting"></a>

This section covers some basic failure modes and troubleshooting for DynamoDB exports\.

If an error occurs during an export or import, the pipeline status in the AWS Data Pipeline console will display as `ERROR`\. If this happens, click the name of the failed pipeline to go to its detail page\. This will show details about all of the steps in the pipeline, and the status of each one\. In particular, examine any execution stack traces that you see\.

Finally, go to your Amazon S3 bucket and look for any export or import log files that were written there\.

The following are some common issues that may cause a pipeline to fail, along with corrective actions\. To diagnose your pipeline, compare the errors you have seen with the issues noted below\.
+ For an import, ensure that the destination table already exists, and the destination table has the same key schema as the source table\. These conditions must be met, or the import will fail\.
+ Ensure that the pipeline has the tag `dynamodbdatapipeline`; otherwise, the Amazon EMR API calls will not succeed\.
+ Ensure that the Amazon S3 bucket you specified has been created, and that you have read and write permissions on it\.
+ The pipeline might have exceeded its execution timeout\. \(You set this parameter when you created the pipeline\.\) For example, you might have set the execution timeout for 1 hour, but the export job might have required more time than this\. Try deleting and then re\-creating the pipeline, but with a longer execution timeout interval this time\.
+ Update the manifest file if you restore from a Amazon S3 bucket that is not the original bucket that the export was performed with \(contains a copy of the export\)\. 
+ You might not have the correct permissions for performing an export or import\. For more information, see [Prerequisites to export and import data](#DataPipelineExportImport.Prereqs)\.
+ You might have reached a resource quota in your AWS account, such as the maximum number of Amazon EC2 instances or the maximum number of AWS Data Pipeline pipelines\. For more information, including how to request increases in these quotas, see [AWS service quotas](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html#limits_ec2) in the *AWS General Reference*\. 

**Note**  
For more details on troubleshooting a pipeline, go to [Troubleshooting](https://docs.aws.amazon.com/datapipeline/latest/DeveloperGuide/dp-troubleshooting.html) in the *AWS Data Pipeline Developer Guide*\.

## Predefined templates for AWS Data Pipeline and DynamoDB<a name="DynamoDBPipeline.Templates"></a>

If you would like a deeper understanding of how AWS Data Pipeline works, we recommend that you consult the *AWS Data Pipeline Developer Guide*\. This guide contains step\-by\-step tutorials for creating and working with pipelines; you can use these tutorials as starting points for creating your own pipelines\. We recommend that you read the AWS Data Pipeline tutorial, which walks you through the steps required to create an import and export pipeline that you can customize for your requirements\. See [ Tutorial: Amazon DynamoDB import and export using AWS Data Pipeline](https://docs.aws.amazon.com/datapipeline/latest/DeveloperGuide/dp-importexport-ddb.html) in the *AWS Data Pipeline Developer Guide*\.

AWS Data Pipeline offers several templates for creating pipelines; the following templates are relevant to DynamoDB\.

### Exporting data between DynamoDB and Amazon S3<a name="DynamoDBPipeline.ExportDDBToS3"></a>

**Note**  
 DynamoDB Console now supports its own Export to Amazon S3 flow, however it is not compatible with AWS Data Pipeline import flow\. For more information, see [DynamoDB data export to Amazon S3: how it works](S3DataExport.HowItWorks.md) and the blog post [ Export Amazon DynamoDB table data to your data lake in Amazon S3, no code writing required](https://aws.amazon.com/blogs/aws/new-export-amazon-dynamodb-table-data-to-data-lake-amazon-s3)\. 

The AWS Data Pipeline console provides two predefined templates for exporting data between DynamoDB and Amazon S3\. For more information about these templates, see the following sections of the *AWS Data Pipeline Developer Guide*:
+ [Export DynamoDB to Amazon S3](https://docs.aws.amazon.com/datapipeline/latest/DeveloperGuide/dp-template-exportddbtos3.html)
+ [Export Amazon S3 to DynamoDB](https://docs.aws.amazon.com/datapipeline/latest/DeveloperGuide/dp-template-exports3toddb.html)