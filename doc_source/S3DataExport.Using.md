# Using DynamoDB table exports with other AWS services<a name="S3DataExport.Using"></a>

Once your DynamoDB table data is exported to Amazon S3, you can process it from a variety of other AWS services\. The following sections detail some of the options for querying your exported data\.

## Using exports with Amazon Athena<a name="S3DataExport.Using.Athena"></a>

Amazon Athena is an interactive query service that makes it easy to analyze data in Amazon S3 using standard SQL\. Athena is serverless, so there is no infrastructure to manage and you pay only for the queries that you run\. For more information, see the [Amazon Athena User Guide](https://docs.aws.amazon.com/athena/latest/ug/)\.

## Using exports with AWS Glue<a name="S3DataExport.Using.Glue"></a>

AWS Glue is a fully managed extract, transform, and load \(ETL\) service that makes it easy to load your data for analytics\. You can create and run an ETL job with a few clicks in the AWS Glue visual editor\. You simply point AWS Glue to your data and AWS Glue discovers your data and stores the associated metadata \(such as table definition and schema\) in the AWS Glue Data Catalog\. Once catalogued, your data is immediately searchable, queryable, and available for ETL\. For more information, see the [AWS Glue Developer Guide](https://docs.aws.amazon.com/glue/latest/dg/)\.

## Using exports with AWS Lake Formation<a name="S3DataExport.Using.LakeFormation"></a>

AWS Lake Formation makes it easy to set up a secure data lake in days\. A data lake is a centralized, curated, and secured repository that stores all your data, both in its original form and prepared for analysis\. A data lake enables you to break down data silos and combine different types of analytics to gain insights and guide better business decisions\.

Creating a data lake with Lake Formation is as simple as defining data sources and what data access and security policies you want to apply\. Lake Formation then helps you collect and catalog data from databases and object storage, move the data into your new Amazon S3 data lake, clean and classify your data using machine learning algorithms, and secure access to your sensitive data\. For more information, see the [AWS Lake Formation Developer Guide](https://docs.aws.amazon.com/lake-formation/latest/dg/)\.