# Cross\-Region Replication<a name="Streams.CrossRegionRepl"></a>

**Important**  
AWS previously provided a cross\-region replication solution based on AWS CloudFormation\. This solution has now been deprecated in favor of an open source command line tool\. For more information, please refer to the detailed instructions on GitHub:   
[https://github\.com/awslabs/dynamodb\-cross\-region\-library/blob/master/README\.md](https://github.com/awslabs/dynamodb-cross-region-library/blob/master/README.md)

The DynamoDB cross\-region replication solution uses the Amazon DynamoDB Cross\-Region Replication Library\. This library uses DynamoDB Streams to keep DynamoDB tables in sync across multiple regions in near real time\. When you write to a DynamoDB table in one region, those changes are automatically propagated by the Cross\-Region Replication Library to your tables in other regions\.

You can leverage the Cross\-Region Replication Library in your own applications, to build your own replication solutions with DynamoDB Streams\. For more information, and to download the source code, go to the following GitHub repository:

+  [https://github\.com/awslabs/dynamodb\-cross\-region\-library](https://github.com/awslabs/dynamodb-cross-region-library) 