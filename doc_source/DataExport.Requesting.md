# Requesting a table export in DynamoDB<a name="DataExport.Requesting"></a>

DynamoDB table exports allow you to export table data to an Amazon S3 bucket, enabling you to perform analytics and complex queries on your data using other AWS services such as Athena, AWS Glue, and Lake Formation\. You can request a table export using the AWS Management Console, the AWS CLI, or the DynamoDB API\.

If you want to use the AWS CLI, you must configure it first\. For more information, see [Accessing DynamoDB](AccessingDynamoDB.md)\.

**Topics**
+ [Amazon S3 setup and permissions](#DataExport.Requesting.Permissions)
+ [Requesting an export using the AWS Management Console](#DataExport.Requesting.Console)
+ [Getting details about past exports in the AWS Management Console](#DataExport.Requesting.Console.Details)
+ [Requesting an export using the AWS CLI](#DataExport.Requesting.CLI)
+ [Getting details about past exports in the AWS CLI](#DataExport.Requesting.CLI.Details)

## Amazon S3 setup and permissions<a name="DataExport.Requesting.Permissions"></a>

You can export your table data to any Amazon S3 bucket you have permission to write to\. The destination bucket does not need to be in the same region or have the same owner as the source table\. Your AWS Identity and Access Management \(IAM\) policy should allow the `s3:AbortMultipartUpload`, `s3:PutObject`, and `s3:PutObjectAcl` actions, in addition to the `dynamodb:ExportTableToPointInTime` permission, as shown in the following example\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowDynamoDBExportAction",
            "Effect": "Allow",
            "Action": "dynamodb:ExportTableToPointInTime",
            "Resource": "arn:aws:dynamodb:us-east-1:111122223333:table/my-table"
        },
        {
            "Sid": "AllowWriteToDestinationBucket",
            "Effect": "Allow",
            "Action": [
                "s3:AbortMultipartUpload",
                "s3:PutObject",
                "s3:PutObjectAcl"
            ],
            "Resource": "arn:aws:s3:::your-bucket/*"
        }
    ]
}
```

The following code is an example S3 bucket policy \(in the target account\)\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "ExampleStatement",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::123456789012:user/Dave"
            },
            "Action": [
                "s3:AbortMultipartUpload",
                "s3:PutObject",
                "s3:PutObjectAcl"
            ],
            "Resource": "arn:aws:s3:::awsexamplebucket1/*"
        }
    ]
}
```

Revoking these permissions while an export is in progress will result in partial files\.

## Requesting an export using the AWS Management Console<a name="DataExport.Requesting.Console"></a>

The following example demonstrates how to use the DynamoDB console to export an existing table named `MusicCollection`\.

**Note**  
This procedure assumes that you have enabled point\-in\-time recovery\. To enable it for the `MusicCollection` table, on the table's **Overview** tab, in the **Table details** section, choose **Enable** for **Point\-in\-time recovery**\.

**To request a table export**

1. Sign in to the AWS Management Console and open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. In the navigation pane on the left side of the console, choose **Exports to S3**\.

1. Choose **Export to S3**\.

1. Choose a source table and destination S3 bucket\. If the desination bucket is owned by your account, you can use the **Browse S3** button to find it\. Otherwise, enter the URL of the bucket using the `s3://bucketname/prefix format.` the **prefix** is an optional folder to help keep your desination bucket organized\.

1. By default, your table will be exported in DynamoDB JSON format from the latest restorable time in the point in time recovery window and encrypted using an Amazon S3 key \(SSE\-S3\)\. If you want to change these settings, expand the **Additional settings** section of the page and enter your desired export settings\.
**Note**  
If you choose to encrypt your export using a key protected by AWS Key Management Service \(AWS KMS\), the key must be in the same region as the destination S3 bucket\.

1. Click the **Export** button to begin the export\.

## Getting details about past exports in the AWS Management Console<a name="DataExport.Requesting.Console.Details"></a>

You can find information about export tasks you've run in the past by clicking **Streams and exports** in the navigation sidebar, then selecting the **Exports** tab\. This tab contains a list of all exports you've created in the past 90 days\. Selecting the ARN of a task listed in the Exports tab will retrieve information about that export, including any advanced configuration settings you chose\. Note that although export task metadata expires after 90 days and jobs older than that are no longer found in this list, the objects in your S3 bucket remain as long as their bucket policies allow\. DynamoDB never deletes any of the objects it creates in your S3 bucket during an export\.

## Requesting an export using the AWS CLI<a name="DataExport.Requesting.CLI"></a>

The following example shows how to use the AWS CLI to export an existing table named `MusicCollection` to an S3 bucket called `ddb-export-musiccollection`\.

**Note**  
This procedure assumes that you have enabled point\-in\-time recovery\. To enable it for the `MusicCollection` table, run the following command\.  

```
aws dynamodb update-continuous-backups \
    --table-name MusicCollection \
    --point-in-time-recovery-specification PointInTimeRecoveryEnabled=True
```

The following command exports the `MusicCollection` to an S3 bucket called `ddb-export-musiccollection` with a prefix of `2020-Nov`\. Table data will be exported in DynamoDB JSON format from a specific time within the point in time recovery window and encrypted using an Amazon S3 key \(SSE\-S3\)\.

```
aws dynamodb export-table-to-point-in-time \
    --table-arn arn:aws:dynamodb:us-west-2:123456789012:table/MusicCollection \
    --s3-bucket ddb-export-musiccollection \
    --s3-prefix 2020-Nov \
    --export-format DYNAMODB_JSON \
    --export-time 1604632434 \
    --s3-sse-algorithm AES256
```

**Note**  
If you choose to encrypt your export using a key protected by AWS Key Management Service \(AWS KMS\), the key must be in the same region as the destination S3 bucket\.

## Getting details about past exports in the AWS CLI<a name="DataExport.Requesting.CLI.Details"></a>

You can find information about export tasks you've run in the past by using the `list-exports` command\. This command returns a list of all exports you've created in the past 90 days\. Note that although export task metadata expires after 90 days and jobs older than that are no longer returned by the `list-exports` command, the objects in your S3 bucket remain as long as their bucket policies allow\. DynamoDB never deletes any of the objects it creates in your S3 bucket during an export\.

In the following example, we use the optional `table-arn` parameter to list only exports of a specific table\.

```
aws dynamodb list-exports \
    --table-arn arn:aws:dynamodb:us-east-1:123456789012:table/ProductCatalog
```

To retrieve detailed information about a specific export task, including any advanced configuration settings, use the `describe-export` command\.

```
aws dynamodb describe-export \
    --export-arn arn:aws:dynamodb:us-east-1:123456789012:table/ProductCatalog/export/01234567890123-a1b2c3d4
```