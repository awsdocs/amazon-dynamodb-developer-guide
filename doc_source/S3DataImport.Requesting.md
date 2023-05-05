# Requesting a table import in DynamoDB<a name="S3DataImport.Requesting"></a>

DynamoDB import allows you to import data from an Amazon S3 bucket to a new DynamoDB table\. You can request a table import using the [DynamoDB console](https://console.aws.amazon.com/), the [CLI](Tools.CLI.md), [CloudFormation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-dynamodb-table.html) or the [DynamoDB API\.](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/Welcome.html)

If you want to use the AWS CLI, you must configure it first\. For more information, see [Accessing DynamoDB](AccessingDynamoDB.md)\.

**Note**  
The Import Table feature interacts with multiple different AWS Services such as Amazon S3 and CloudWatch\. Before you begin an import, make sure that the user or role that invokes the import APIs has permissions to all services and resources the feature depends on\. 
 Do not modify the Amazon S3 objects while the import is in progress, as this can cause the operation to fail or be cancelled\.
For more information on errors and troubleshooting, see [Import format quotas and validation](S3DataImport.Validation.md)

**Topics**
+ [Setting up IAM permissions](#DataImport.Requesting.Permissions)
+ [Requesting an import using the AWS Management Console](#S3DataImport.Requesting.Console)
+ [Getting details about past imports in the AWS Management Console](#S3DataImport.Requesting.Console.Details)
+ [Requesting an import using the AWS CLI](#S3DataImport.Requesting.CLI)
+ [Getting details about past imports in the AWS CLI](#S3DataImport.Requesting.CLI.Details)

## Setting up IAM permissions<a name="DataImport.Requesting.Permissions"></a>

You can import data from any Amazon S3 bucket you have permission to read from\. The source bucket does not need to be in the same Region or have the same owner as the source table\. Your AWS Identity and Access Management \(IAM\) must include the relevant actions on the source Amazon S3 bucket, and required CloudWatch permissions for providing debugging information\. An example policy is shown below\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowDynamoDBImportAction",
            "Effect": "Allow",
            "Action": [
                "dynamodb:ImportTable",
                "dynamodb:DescribeImport",
                "dynamodb:ListImports"
            ],
            "Resource": "arn:aws:dynamodb:us-east-1:111122223333:table/my-table"
        },
        {
            "Sid": "AllowS3Access",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": "arn:aws:s3:::your-bucket/*"
        },
        {
            "Sid": "AllowCloudwatchAccess",
            "Effect": "Allow",
            "Action": [
                "logs:CreateLogGroup",
                "logs:CreateLogStream",
                "logs:DescribeLogGroups",
                "logs:DescribeLogStreams",
                "logs:PutLogEvents",
                "logs:PutRetentionPolicy"
            ],
            "Resource": "arn:aws:logs:us-east-1:111122223333:log-group:/aws-dynamodb/*"
        }
    ]
}
```

### Amazon S3 permissions<a name="DataImport.Requesting.Permissions.s3"></a>

When starting an import on an Amazon S3 bucket source that is owned by another account, ensure that the role or user has access to the Amazon S3 objects\. You can check that by executing an Amazon S3 `GetObject` command and using the credentials\. When using the API, the Amazon S3 bucket owner parameter defaults to the current user’s account ID\. For cross account imports, ensure that this parameter is correctly populated with the bucket owner’s account ID\. The following code is an example Amazon S3 bucket policy in the source account\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {"Sid": "ExampleStatement",
            "Effect": "Allow",
            "Principal": {"AWS": "arn:aws:iam::123456789012:user/Dave"
            },
            "Action": [
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": "arn:aws:s3:::awsexamplebucket1/*"
        }
    ]
}
```

### KMS<a name="DataImport.Requesting.Permissions.kms"></a>

When creating the new table for import, if you select an encryption at rest key that is not owned by DynamoDB then you must provide the AWS KMS permissions required to operate a DynamoDB table encrypted with CM\-CMK\. For more information see [Authorizing use of your AWS KMS key](encryption.usagenotes.html#dynamodb-kms-authz)\. If the Amazon S3 objects are encrypted with SSE\-KMS, ensure that the role or user initiating the import has access to decrypt using the AWS KMS key\. This feature does not support SSE\-C encrypted Amazon S3 objects\. 

### CloudWatch permissions<a name="DataImport.Requesting.Permissions.cw"></a>

The role or user that is initiating the import will need create and manage permissions for the log group and log streams associated with the import\. 

## Requesting an import using the AWS Management Console<a name="S3DataImport.Requesting.Console"></a>

The following example demonstrates how to use the DynamoDB console to import existing data to a new table named `MusicCollection`\.

**To request a table import**

1. Sign in to the AWS Management Console and open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. In the navigation pane on the left side of the console, choose **Import from S3**\.

1. On the page that appears, select **Import from S3**\.

1. Choose **Import from S3**\.

1. Enter the Amazon S3 source URL\.

   If the source bucket is owned by your account, you can find it by using the **Browse S3** button\. Alternately, you can enter the URL of the bucket in the `s3://bucket/prefix` format\. The `prefix` is an Amazon S3 key prefix that is either the Amazon S3 object name that you want to import, or the key prefix shared by all the Amazon S3 objects that you want to import\.

1. Specify if you are the **S3 bucket owner**\. If the source bucket is owned by a different account, select **A different AWS account**\. Then enter the account ID of the bucket owner\.

1. Under **Import file compression**, select either **No compression**, **GZIP** or **ZSTD** as appropriate\.

1. Select the appropriate Import file format\. The options are **DynamoDB JSON**, **Amazon Ion** or **CSV**\. If you select **CSV**, you will have two additional options: **CSV header** and **CSV delimiter character**\.

   For **CSV header**, choose if the header will either be taken from the first line of the file or be customized\. If you select **Customize your headers**, you can specify the header values you want to import with\. CSV Headers specified by this method are case\-sensitive and are expected to contain the keys of the target table\. 

   For **CSV delimiter character**, you set the character which will separate items\. Comma is selected by default\. If you select **Custom delimiter character**, the delimiter must match the regex pattern: `[,;:|\t ]`\.

1. Select the **Next** button and select the options for the new table that will be created to store your data\. 
**Note**  
Primary Key and Sort Key must match the attributes in the file, or the import will fail\. The attributes are case sensitive\.

1. Select **Next** again to review your import options, then click **Import** to begin the import task\. You will first see your new table listed in the “Tables” with the status “Creating”\. At this time the table is not accessible\.

1. Once the import completes, the status will show as "Active" and you can start using the table\.

## Getting details about past imports in the AWS Management Console<a name="S3DataImport.Requesting.Console.Details"></a>

You can find information about import tasks you've run in the past by clicking **Import from S3** in the navigation sidebar, then selecting the **Imports** tab\. The import panel contains a list of all imports you've created in the past 90 days\. Selecting the ARN of a task listed in the Imports tab will retrieve information about that import, including any advanced configuration settings you chose\.

## Requesting an import using the AWS CLI<a name="S3DataImport.Requesting.CLI"></a>

The following example imports CSV formatted data from an S3 bucket called bucket with a prefix of prefix to a new table called target\-table\.

```
aws dynamodb import-table --s3-bucket-source S3Bucket=bucket,S3KeyPrefix=prefix \ 
            --input-format CSV --table-creation-parameters '{"TableName":"target-table","KeySchema":  \
            [{"AttributeName":"hk","KeyType":"HASH"}],"AttributeDefinitions":[{"AttributeName":"hk","AttributeType":"S"}],"BillingMode":"PAY_PER_REQUEST"}' \ 
            --input-format-options '{"Csv": {"HeaderList": ["hk", "title", "artist", "year_of_release"], "Delimiter": ";"}}'
```

**Note**  
If you choose to encrypt your import using a key protected by AWS Key Management Service \(AWS KMS\), the key must be in the same Region as the destination Amazon S3 bucket\.

## Getting details about past imports in the AWS CLI<a name="S3DataImport.Requesting.CLI.Details"></a>

You can find information about import tasks you've run in the past by using the `list-imports` command\. This command returns a list of all imports you've created in the past 90 days\. Note that although import task metadata expires after 90 days and jobs older than that are no longer found on this list, DynamoDB does not delete any of the objects in your Amazon S3 bucket or the table created during import\.

```
aws dynamodb list-imports
```

To retrieve detailed information about a specific import task, including any advanced configuration settings, use the `describe-import` command\.

```
aws dynamodb describe-import \
    --import-arn arn:aws:dynamodb:us-east-1:123456789012:table/ProductCatalog/exp
```