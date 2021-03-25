# Exporting DynamoDB table data to Amazon S3<a name="DataExport"></a>

Using DynamoDB table export, you can export data from an Amazon DynamoDB table from any time within your point\-in\-time recovery window to an Amazon S3 bucket\. Exporting a DynamoDB table to an S3 bucket enables you to perform analytics and complex queries on your data using other AWS services such as Athena, AWS Glue, and Lake Formation\. DynamoDB table export is a fully managed solution for exporting DynamoDB tables at scale, and is much faster than other workarounds involving table scans\. For more information, see [DynamoDB data export to Amazon S3: how it works](DataExport.HowItWorks.md)\.

Exporting a table does not consume read capacity on the table, and has no impact on table performance and availability\. You can export table data to an S3 bucket owned by another AWS account, and to a different region than the one your table is in\. Your data is always encrypted end\-to\-end\.

You can export a DynamoDB table using the AWS Management Console, the AWS Command Line Interface, or the DynamoDB API\. For more information, see [Requesting a table export in DynamoDB](DataExport.Requesting.md)\.

For more information about AWS Region availability and pricing, see [Amazon DynamoDB pricing](https://aws.amazon.com/dynamodb/pricing)\.

**Topics**
+ [DynamoDB data export to Amazon S3: how it works](DataExport.HowItWorks.md)
+ [Requesting a table export in DynamoDB](DataExport.Requesting.md)
+ [DynamoDB table export output format](DataExport.Output.md)
+ [Using DynamoDB table exports with other AWS services](#DataExport.Using)

## Using DynamoDB table exports with other AWS services<a name="DataExport.Using"></a>

Once your DynamoDB table data is exported to Amazon S3, you can process it from a variety of other AWS services\. The following sections detail some of the options for querying your exported data\.

### Using exports with Amazon Athena<a name="DataExport.Using.Athena"></a>

Amazon Athena is an interactive query service that makes it easy to analyze data in Amazon S3 using standard SQL\. Athena is serverless, so there is no infrastructure to manage and you pay only for the queries that you run\. For more information, see the [Amazon Athena User Guide](https://docs.aws.amazon.com/athena/latest/ug/)\.

### Using exports with AWS Glue<a name="DataExport.Using.Glue"></a>

AWS Glue is a fully managed extract, transform, and load \(ETL\) service that makes it easy to load your data for analytics\. You can create and run an ETL job with a few clicks in the AWS Glue visual editor\. You simply point AWS Glue to your data and AWS Glue discovers your data and stores the associated metadata \(such as table definition and schema\) in the AWS Glue Data Catalog\. Once catalogued, your data is immediately searchable, queryable, and available for ETL\. For more information, see the [AWS Glue Developer Guide](https://docs.aws.amazon.com/glue/latest/dg/)\.

### Using exports with AWS Lake Formation<a name="DataExport.Using.LakeFormation"></a>

AWS Lake Formation makes it easy to set up a secure data lake in days\. A data lake is a centralized, curated, and secured repository that stores all your data, both in its original form and prepared for analysis\. A data lake enables you to break down data silos and combine different types of analytics to gain insights and guide better business decisions\.

Creating a data lake with Lake Formation is as simple as defining data sources and what data access and security policies you want to apply\. Lake Formation then helps you collect and catalog data from databases and object storage, move the data into your new Amazon S3 data lake, clean and classify your data using machine learning algorithms, and secure access to your sensitive data\. For more information, see the [AWS Lake Formation Developer Guide](https://docs.aws.amazon.com/lake-formation/latest/dg/)\.