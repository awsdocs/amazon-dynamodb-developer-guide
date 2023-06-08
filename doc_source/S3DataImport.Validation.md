# Import format quotas and validation<a name="S3DataImport.Validation"></a>

## Import quotas<a name="S3DataImport.Validation.limits"></a>

DynamoDB Import from Amazon S3 can support up to 50 concurrent import jobs with a total import source object size of 15TB at a time in us\-east\-1, us\-west\-2, and eu\-west\-1 regions\. In all other regions, up to 50 concurrent import tasks with a total size of 1TB is supported\. Each import job can take up to 5000 Amazon S3 objects in all regions\. For more details on DynamoDB limits, see [Service Quotas](ServiceQuotas.html)\.

## Validation errors<a name="S3DataImport.Validation.Errors"></a>

During the import process, DynamoDB may encounter errors while parsing your data\. For each error, DynamoDB emits a CloudWatch log and keeps a count of the total number of errors encountered\. If the Amazon S3 object itself is malformed or if its contents cannot form a DynamoDB item, then we may skip processing the remaining portion of the object\.

**Note**  
If the Amazon S3 data source has multiple items that share the same key, the items will overwrite until one remains\. This can appear as if 1 item was imported and the others were ignored\. The duplicate items will be overwritten in random order, are not counted as errors, and are not emitted to CloudWatch logs\.  
Once the import is complete you can see the total count of items imported, total count of errors, and total count of items processed\. For further troubleshooting you can also check the total size of items imported and total size of data processed\.

There are three categories of import errors: API validation errors, data validation errors, and configuration errors\.

### API validation errors<a name="S3DataImport.Validation.Errors.API"></a>

API validation errors are item\-level errors from the sync API\. Common causes are permissions issues, missing required parameters and parameter validation failures\. Details on why the API call failed are contained in the exceptions thrown by the `ImportTable` request\.

### Data validation errors<a name="S3DataImport.Validation.Errors.Data"></a>

Data validation errors can occur at either the item level or file level\. During import, items are validated based on DynamoDB rules before importing into the target table\. When an item fails validation and is not imported, the import job skips over that item and continues on with the next item\. At the end of job, the import status is set to FAILED with a FailureCode, ItemValidationError and the FailureMessage "Some of the items failed validation checks and were not imported\. Please check CloudWatch error logs for more details\."

 Common causes for data validation errors include objects being unparsable, objects being in the incorrect format \(input specifies DYNAMODB\_JSON but the object is not in DYNAMODB\_JSON\), and schema mismatch with specified source table keys\.

### Configuration errors<a name="S3DataImport.Validation.Errors.Configuration"></a>

Configuration errors are typically workflow errors due to permission validation\. The Import workflow checks some permissions after accepting the request\. If there are issues calling any of the required dependencies like Amazon S3 or CloudWatch the process marks the import status as FAILED\. The `failureCode` and `failureMessage` point to the reason for failure\. Where applicable, the failure message also contains the request id that you can use to investigate the reason for failure in CloudTrail\.

Common configuration errors include having the wrong URL for the Amazon S3 bucket, and not having permission to access the Amazon S3 bucket, CloudWatch Logs, and AWS KMS keys used to decrypt the Amazon S3 object\. For more information see [Using and data keys](encryption.usagenotes.html#dynamodb-kms)\. 

### Validating source Amazon S3 objects<a name="S3DataImport.Validation.Errors.S3Objects"></a>

In order to validate source S3 objects, take the following steps\.

1. Validate the data format and compression type 
   + Make sure that all matching Amazon S3 objects under the specified prefix have the same format \(DYNAMODB\_JSON, DYNAMODB\_ION, CSV\)
   + Make sure that all matching Amazon S3 objects under the specified prefix are compressed the same way \(GZIP, ZSTD, NONE\)
**Note**  
The Amazon S3 objects do not need to have the corresponding extension \(\.csv / \.json / \.ion / \.gz / \.zstd etc\) as the input format specified in ImportTable call takes precedence\.

1. Validate that the import data conforms to the desired table schema
   + Make sure that each item in the source data has the primary key\. A sort key is optional for imports\.
   + Make sure that the attribute type associated with the primary key and any sort key matches the attribute type in the Table and the GSI schema, as specified in table creation parameters

### Troubleshooting<a name="S3DataImport.Validation.Troubleshooting"></a>

#### CloudWatch logs<a name="S3DataImport.Validation.Troubleshooting.Cloudwatch"></a>

For Import jobs that fail, detailed error messages are posted to CloudWatch logs\. To access these logs, first retrieve the ImportArn from the output and describe\-import using this command:

```
aws dynamodb describe-import --import-arn arn:aws:dynamodb:us-east-1:ACCOUNT:table/target-table/import/01658528578619-c4d4e311
}
```

Example output:

```
aws dynamodb describe-import --import-arn "arn:aws:dynamodb:us-east-1:531234567890:table/target-table/import/01658528578619-c4d4e311"
{
    "ImportTableDescription": {
        "ImportArn": "arn:aws:dynamodb:us-east-1:ACCOUNT:table/target-table/import/01658528578619-c4d4e311",
        "ImportStatus": "FAILED",
        "TableArn": "arn:aws:dynamodb:us-east-1:ACCOUNT:table/target-table",
        "TableId": "7b7ecc22-302f-4039-8ea9-8e7c3eb2bcb8",
        "ClientToken": "30f8891c-e478-47f4-af4a-67a5c3b595e3",
        "S3BucketSource": {
            "S3BucketOwner": "ACCOUNT",
            "S3Bucket": "my-import-source",
            "S3KeyPrefix": "import-test"
        },
        "ErrorCount": 1,
        "CloudWatchLogGroupArn": "arn:aws:logs:us-east-1:ACCOUNT:log-group:/aws-dynamodb/imports:*",
        "InputFormat": "CSV",
        "InputCompressionType": "NONE",
        "TableCreationParameters": {
            "TableName": "target-table",
            "AttributeDefinitions": [
                {
                    "AttributeName": "pk",
                    "AttributeType": "S"
                }
            ],
            "KeySchema": [
                {
                    "AttributeName": "pk",
                    "KeyType": "HASH"
                }
            ],
            "BillingMode": "PAY_PER_REQUEST"
        },
        "StartTime": 1658528578.619,
        "EndTime": 1658528750.628,
        "ProcessedSizeBytes": 70,
        "ProcessedItemCount": 1,
        "ImportedItemCount": 0,
        "FailureCode": "ItemValidationError",
        "FailureMessage": "Some of the items failed validation checks and were not imported. Please check CloudWatch error logs for more details."
    }
}
```

Retrieve the log group and the import id from the above response and use it to retrieve the error logs\. The import ID is the last path element of the `ImportArn` field\. The log group name is `/aws-dynamodb/imports`\. The error log stream name is `import-id/error`\. For this example, it would be `01658528578619-c4d4e311/error`\.

#### Missing the key pk in the item<a name="S3DataImport.Validation.Troubleshooting.Missing"></a>

If the source S3 object does not contain the primary key that was provided as a parameter, the import will fail\. For example, when you define the primary key for the import as column name “pk”\.

```
aws dynamodb import-table —s3-bucket-source S3Bucket=my-import-source,S3KeyPrefix=import-test.csv \ 
            —input-format CSV --table-creation-parameters '{"TableName":"target-table","KeySchema":  \
            [{"AttributeName":"pk","KeyType":"HASH"}],"AttributeDefinitions":[{"AttributeName":"pk","AttributeType":"S"}],"BillingMode":"PAY_PER_REQUEST"}'
```

The column “pk” is missing from the the source object `import-test.csv` which has the following contents:

```
title,artist,year_of_release
The Dark Side of the Moon,Pink Floyd,1973
```

This import will fail due to item validation error because of the missing primary key in the data source\.

Example CloudWatch error log:

```
aws logs get-log-events —log-group-name /aws-dynamodb/imports —log-stream-name 01658528578619-c4d4e311/error
{
"events": [
{
"timestamp": 1658528745319,
"message": "{\"itemS3Pointer\":{\"bucket\":\"my-import-source\",\"key\":\"import-test.csv\",\"itemIndex\":0},\"importArn\":\"arn:aws:dynamodb:us-east-1:531234567890:table/target-table/import/01658528578619-c4d4e311\",\"errorMessages\":[\"One or more parameter values were invalid: Missing the key pk in the item\"]}",
"ingestionTime": 1658528745414
}
],
"nextForwardToken": "f/36986426953797707963335499204463414460239026137054642176/s",
"nextBackwardToken": "b/36986426953797707963335499204463414460239026137054642176/s"
}
```

This error log indicates that “One or more parameter values were invalid: Missing the key pk in the item”\. Since this import job failed, the table “target\-table” now exists and is empty because no items were imported\. The first item was processed and the object failed Item Validation\. 

To fix the issue, first delete “target\-table” if it is no longer needed\. Then either use a primary key column name that exists in the source object, or update the source data to:

```
pk,title,artist,year_of_release
Albums::Rock::Classic::1973::AlbumId::ALB25,The Dark Side of the Moon,Pink Floyd,1973
```

#### Target table exists<a name="S3DataImport.Validation.Troubleshooting.TargetTable"></a>

When you start an import job and receive a response as follows:

```
An error occurred (ResourceInUseException) when calling the ImportTable operation: Table already exists: target-table
```

To fix this error, you will need to choose a table name that doesn’t already exist and retry the import\. 

#### The specified bucket does not exist<a name="S3DataImport.Validation.Troubleshooting.Bucket"></a>

If the source bucket does not exist, the import will fail and log the error message details in CloudWatch\. 

Example describe import:

```
aws dynamodb —endpoint-url $ENDPOINT describe-import —import-arn "arn:aws:dynamodb:us-east-1:531234567890:table/target-table/import/01658530687105-e6035287"
{
"ImportTableDescription": {
"ImportArn": "arn:aws:dynamodb:us-east-1:ACCOUNT:table/target-table/import/01658530687105-e6035287",
"ImportStatus": "FAILED",
"TableArn": "arn:aws:dynamodb:us-east-1:ACCOUNT:table/target-table",
"TableId": "e1215a82-b8d1-45a8-b2e2-14b9dd8eb99c",
"ClientToken": "3048e16a-069b-47a6-9dfb-9c259fd2fb6f",
"S3BucketSource": {
"S3BucketOwner": "531234567890",
"S3Bucket": "BUCKET_DOES_NOT_EXIST",
"S3KeyPrefix": "import-test"
},
"ErrorCount": 0,
"CloudWatchLogGroupArn": "arn:aws:logs:us-east-1:ACCOUNT:log-group:/aws-dynamodb/imports:*",
"InputFormat": "CSV",
"InputCompressionType": "NONE",
"TableCreationParameters": {
"TableName": "target-table",
"AttributeDefinitions": [
{
"AttributeName": "pk",
"AttributeType": "S"
}
],
"KeySchema": [
{
"AttributeName": "pk",
"KeyType": "HASH"
}
],
"BillingMode": "PAY_PER_REQUEST"
},
"StartTime": 1658530687.105,
"EndTime": 1658530701.873,
"ProcessedSizeBytes": 0,
"ProcessedItemCount": 0,
"ImportedItemCount": 0,
"FailureCode": "S3NoSuchBucket",
"FailureMessage": "The specified bucket does not exist (Service: Amazon S3; Status Code: 404; Error Code: NoSuchBucket; Request ID: Q4W6QYYFDWY6WAKH; S3 Extended Request ID: ObqSlLeIMJpQqHLRX2C5Sy7n+8g6iGPwy7ixg7eEeTuEkg/+chU/JF+RbliWytMlkUlUcuCLTrI=; Proxy: null)"
}
}
```

The `FailureCode` is `S3NoSuchBucket`, with `FailureMessag` containing details such as request id and the service that threw the error\. Since the error was caught before the data was imported into the table, a new DynamoDB table is not created\. In some cases, when these errors are encountered after the data import has started, the table with partially imported data is retained\. 

To fix this error, make sure that the source Amazon S3 bucket exists and then restart the import process\.