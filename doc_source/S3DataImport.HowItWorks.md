# DynamoDB data import from Amazon S3: how it works<a name="S3DataImport.HowItWorks"></a>

To import data into DynamoDB, your data must be in an Amazon S3 bucket in CSV, DynamoDB JSON, or Amazon Ion format\. Data can be compressed in ZSTD or GZIP format, or can be directly imported in uncompressed form\. Source data can either be a single Amazon S3 object or multiple Amazon S3 objects that use the same prefix\.

Your data will be imported into a new DynamoDB table, which will be created when you initiate the import request\. You can create this table with secondary indexes, then query and update your data across all primary and secondary indexes as soon as the import is complete\. You can also add a global table replica after the import is complete\. 

**Note**  
During the Amazon S3 import process, DynamoDB creates a new target table that will be imported into\. Import into existing tables is not currently supported by this feature\.

The following video is an introduction to importing directly from Amazon S3 into DynamoDB\.

[![AWS Videos](http://img.youtube.com/vi/https://www.youtube.com/embed/fqq0CMOnOaI/0.jpg)](http://www.youtube.com/watch?v=https://www.youtube.com/embed/fqq0CMOnOaI)

Import from Amazon S3 does not consume write capacity on the new table, so you do not need to provision any extra capacity for importing data into DynamoDB\. Data import pricing is based on the uncompressed size of the source data in Amazon S3, that is processed as a result of the import\. Items that are processed but fail to load into the table due to formatting or other inconsistencies in the source data are also billed as part of the import process\. See [ Amazon DynamoDB pricing](https://aws.amazon.com/dynamodb/pricing) for details\.

You can import data from an Amazon S3 bucket owned by a different account if you have the correct permissions to read from that specific bucket\. The new table may also be in a different Region from the source Amazon S3 bucket\. For more information, see [Amazon Simple Storage Service setup and permissions ](https://docs.aws.amazon.com/AmazonS3/latest/userguide/example-walkthroughs-managing-access.html)\.

Import times are directly related to your data’s characteristics in Amazon S3\. This includes data size, data format, compression scheme, uniformity of data distribution, number of Amazon S3 objects, and other related variables\. In particular, data sets with uniformly distributed keys will be faster to import than skewed data sets\. For example, if your secondary index's key is using the month of the year for partitioning, and all your data is from the month of December, then importing this data may take significantly longer\. 

The attributes associated with keys are expected to be unique on the base table\. If any keys are not unique, the import will overwrite the associated items until only the last overwrite remains\. For example, if the primary key is the month and multiple items are set to the month of September, each new item will overwrite the previously written items and only one item with the primary key of "month" set to September will remain\. In such cases, the number of items processed in the import table description will not match the number of items in the target table\. 

AWS CloudTrail logs all console and API actions for table import\. For more information, see [Logging DynamoDB operations by using AWS CloudTrail](logging-using-cloudtrail.md)\.

**Topics**
+ [Requesting a table import in DynamoDB](S3DataImport.Requesting.md)
+ [Amazon S3 import formats for DynamoDB](S3DataImport.Format.md)
+ [Import format quotas and validation](S3DataImport.Validation.md)