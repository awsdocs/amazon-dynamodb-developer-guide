# In\-memory acceleration with DynamoDB Accelerator \(DAX\)<a name="DAX"></a>

Amazon DynamoDB is designed for scale and performance\. In most cases, the DynamoDB response times can be measured in single\-digit milliseconds\. However, there are certain use cases that require response times in microseconds\. For these use cases, DynamoDB Accelerator \(DAX\) delivers fast response times for accessing eventually consistent data\. 

DAX is a DynamoDB\-compatible caching service that enables you to benefit from fast in\-memory performance for demanding applications\. DAX addresses three core scenarios:

1.  As an in\-memory cache, DAX reduces the response times of eventually consistent read workloads by an order of magnitude from single\-digit milliseconds to microseconds\. 

1. DAX reduces operational and application complexity by providing a managed service that is API\-compatible with DynamoDB\. Therefore, it requires only minimal functional changes to use with an existing application\.

1. For read\-heavy or bursty workloads, DAX provides increased throughput and potential operational cost savings by reducing the need to overprovision read capacity units\. This is especially beneficial for applications that require repeated reads for individual keys\.

DAX supports server\-side encryption\. With encryption at rest, the data persisted by DAX on disk will be encrypted\. DAX writes data to disk as part of propagating changes from the primary node to read replicas\. For more information, see [DAX encryption at rest](DAXEncryptionAtRest.md)\.

DAX also supports encryption in transit, ensuring that all requests and responses between your application and the cluster are encrypted by transport level security \(TLS\), and connections to the cluster can be authenticated by verification of a cluster x509 certificate\. For more information, see [DAX encryption in transit](DAXEncryptionInTransit.md)\.

**Topics**
+ [Use cases for DAX](#DAX.use-cases)
+ [DAX usage notes](#DAX.usage-notes)
+ [DAX: How it works](DAX.concepts.md)
+ [DAX cluster components](DAX.concepts.cluster.md)
+ [Creating a DAX cluster](DAX.create-cluster.md)
+ [DAX and DynamoDB consistency models](DAX.consistency.md)
+ [Developing with the DynamoDB Accelerator \(DAX\) client](DAX.client.md)
+ [Managing DAX clusters](DAX.cluster-management.md)
+ [Monitoring DAX](DAX.Monitoring.md)
+ [DAX T3/T2 burstable instances](DAX.Burstable.md)
+ [DAX access control](DAX.access-control.md)
+ [DAX encryption at rest](DAXEncryptionAtRest.md)
+ [DAX encryption in transit](DAXEncryptionInTransit.md)
+ [Using service\-linked IAM roles for DAX](using-service-linked-roles.md)
+ [Accessing DAX across AWS accounts](DAX.cross-account-access.md)
+ [DAX cluster sizing guide](DAX.sizing-guide.md)
+ [DAX API reference](DAX.api.md)

## Use cases for DAX<a name="DAX.use-cases"></a>

DAX provides access to eventually consistent data from DynamoDB tables, with microsecond latency\. A Multi\-AZ DAX cluster can serve millions of requests per second\.

DAX is ideal for the following types of applications:
+ Applications that require the fastest possible response time for reads\. Some examples include real\-time bidding, social gaming, and trading applications\. DAX delivers fast, in\-memory read performance for these use cases\.
+ Applications that read a small number of items more frequently than others\. For example, consider an ecommerce system that has a one\-day sale on a popular product\. During the sale, demand for that product \(and its data in DynamoDB\) would sharply increase, compared to all of the other products\. To mitigate the impacts of a "hot" key and a non\-uniform traffic distribution, you could offload the read activity to a DAX cache until the one\-day sale is over\.
+ Applications that are read\-intensive, but are also cost\-sensitive\. With DynamoDB, you provision the number of reads per second that your application requires\. If read activity increases, you can increase your tables' provisioned read throughput \(at an additional cost\)\. Or, you can offload the activity from your application to a DAX cluster, and reduce the number of read capacity units that you need to purchase otherwise\.
+ Applications that require repeated reads against a large set of data\. Such an application could potentially divert database resources from other applications\. For example, a long\-running analysis of regional weather data could temporarily consume all the read capacity in a DynamoDB table\. This situation would negatively impact other applications that need to access the same data\. With DAX, the weather analysis could be performed against cached data instead\.

 DAX is *not* ideal for the following types of applications:
+ Applications that require strongly consistent reads \(or that cannot tolerate eventually consistent reads\)\.
+ Applications that do not require microsecond response times for reads, or that do not need to offload repeated read activity from underlying tables\.
+ Applications that are write\-intensive, or that do not perform much read activity\.
+ Applications that are already using a different caching solution with DynamoDB, and are using their own client\-side logic for working with that caching solution\.

## DAX usage notes<a name="DAX.usage-notes"></a>
+ For a list of AWS Regions where DAX is available, see [Amazon DynamoDB pricing](https://aws.amazon.com/dynamodb/pricing)\.
+ DAX supports applications written in Go, Java, Node\.js, Python, and \.NET, using AWS\-provided clients for those programming languages\.
+ DAX is only available for the EC2\-VPC platform\.
+ The DAX cluster service role policy must allow the `dynamodb:DescribeTable` action in order to maintain metadata about the DynamoDB table\.
+ DAX clusters maintain metadata about the attribute names of items they store\. That metadata is maintained indefinitely \(even after the item has expired or been evicted from the cache\)\. Applications that use an unbounded number of attribute names can, over time, cause memory exhaustion in the DAX cluster\. This limitation applies only to top\-level attribute names, not nested attribute names\. Examples of problematic top\-level attribute names include timestamps, UUIDs, and session IDs\.

  This limitation applies only to attribute names, not their values\. Items like the following are not a problem\.

  ```
  {
      "Id": 123,
      "Title": "Bicycle 123",
      "CreationDate": "2017-10-24T01:02:03+00:00"
  }
  ```

  But items like the following are a problem if there are enough of them and they each have a different timestamp\.

  ```
  {
      "Id": 123,
      "Title": "Bicycle 123",
      "2017-10-24T01:02:03+00:00": "created"
  }
  ```