# Processing DynamoDB Data With Apache Hive on Amazon EMR<a name="EMRforDynamoDB"></a>

Amazon DynamoDB is integrated with Apache Hive, a data warehousing application that runs on Amazon EMR\. Hive can read and write data in DynamoDB tables, allowing you to:
+ Query live DynamoDB data using a SQL\-like language \(HiveQL\)\.
+ Copy data from a DynamoDB table to an Amazon S3 bucket, and vice\-versa\.
+ Copy data from a DynamoDB table into Hadoop Distributed File System \(HDFS\), and vice\-versa\.
+ Perform join operations on DynamoDB tables\.

**Topics**
+ [Overview](#EMRforDynamoDB.Overview)
+ [Tutorial: Working with Amazon DynamoDB and Apache Hive](EMRforDynamoDB.Tutorial.md)
+ [Creating an External Table in Hive](EMRforDynamoDB.ExternalTableForDDB.md)
+ [Processing HiveQL Statements](EMRforDynamoDB.ProcessingHiveQL.md)
+ [Querying Data in DynamoDB](EMRforDynamoDB.Querying.md)
+ [Copying Data to and from Amazon DynamoDB](EMRforDynamoDB.CopyingData.md)
+ [Performance Tuning](EMRforDynamoDB.PerformanceTuning.md)

## Overview<a name="EMRforDynamoDB.Overview"></a>

Amazon EMR is a service that makes it easy to quickly and cost\-effectively process vast amounts of data\. To use Amazon EMR, you launch a managed cluster of Amazon EC2 instances running the Hadoop open source framework\. *Hadoop* is a distributed application that implements the MapReduce algorithm, where a task is mapped to multiple nodes in the cluster\. Each node processes its designated work, in parallel with the other nodes\. Finally, the outputs are reduced on a single node, yielding the final result\.

You can choose to launch your Amazon EMR cluster so that it is persistent or transient:
+ A *persistent* cluster runs until you shut it down\. Persistent clusters are ideal for data analysis, data warehousing, or any other interactive use\.
+ A *transient* cluster runs long enough to process a job flow, and then shuts down automatically\. Transient clusters are ideal for periodic processing tasks, such as running scripts\.

For information about Amazon EMR architecture and administration, see the [Amazon EMR Management Guide](https://docs.aws.amazon.com/ElasticMapReduce/latest/ManagementGuide)\.

When you launch an Amazon EMR cluster, you specify the initial number and type of Amazon EC2 instances\. You also specify other distributed applications \(in addition to Hadoop itself\) that you want to run on the cluster\. These applications include Hue, Mahout, Pig, Spark, and more\.

For information about applications for Amazon EMR, see the [Amazon EMR Release Guide](https://docs.aws.amazon.com/ElasticMapReduce/latest/ReleaseGuide)\.

Depending on the cluster configuration, you might have one or more of the following node types:
+ Leader node — Manages the cluster, coordinating the distribution of the MapReduce executable and subsets of the raw data, to the core and task instance groups\. It also tracks the status of each task performed and monitors the health of the instance groups\. There is only one leader node in a cluster\.
+ Core nodes — Runs MapReduce tasks and stores data using the Hadoop Distributed File System \(HDFS\)\.
+ Task nodes \(optional\) — Runs MapReduce tasks\.