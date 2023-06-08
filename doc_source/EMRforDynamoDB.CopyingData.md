# Copying data to and from Amazon DynamoDB<a name="EMRforDynamoDB.CopyingData"></a>

In the [Tutorial: Working with Amazon DynamoDB and Apache Hive](EMRforDynamoDB.Tutorial.md), you copied data from a native Hive table into an external DynamoDB table, and then queried the external DynamoDB table\. The table is external because it exists outside of Hive\. Even if you drop the Hive table that maps to it, the table in DynamoDB is not affected\.

Hive is an excellent solution for copying data among DynamoDB tables, Amazon S3 buckets, native Hive tables, and Hadoop Distributed File System \(HDFS\)\. This section provides examples of these operations\.

**Topics**
+ [Copying data between DynamoDB and a native Hive table](EMRforDynamoDB.CopyingData.NativeHive.md)
+ [Copying data between DynamoDB and Amazon S3](EMRforDynamoDB.CopyingData.S3.md)
+ [Copying data between DynamoDB and HDFS](EMRforDynamoDB.CopyingData.HDFS.md)
+ [Using data compression](EMRforDynamoDB.CopyingData.Compression.md)
+ [Reading non\-printable UTF\-8 character data](EMRforDynamoDB.CopyingData.NonPrintableData.md)