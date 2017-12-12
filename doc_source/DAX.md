# In\-Memory Acceleration with DAX<a name="DAX"></a>


+ [Use Cases for DAX](#DAX.use-cases)
+ [Usage Notes](#DAX.usage-notes)
+ [Concepts](DAX.concepts.md)
+ [DAX Cluster Components](DAX.concepts.cluster.md)
+ [Creating a DAX Cluster](DAX.create-cluster.md)
+ [DAX and DynamoDB Consistency Models](DAX.consistency.md)
+ [Using the DAX Client in an Application](DAX.client.md)
+ [Managing DAX Clusters](DAX.cluster-management.md)
+ [DAX Access Control](DAX.access-control.md)
+ [DAX API Reference](DAX.api.md)

Amazon DynamoDB is designed for scale and performance\. In most cases, the DynamoDB response times can be measured in single\-digit milliseconds\. However, there are certain use cases that require response times in microseconds\. For these use cases, *DynamoDB Accelerator \(DAX\)* delivers fast response times for accessing eventually consistent data\. 

DAX is a DynamoDB\-compatible caching service that enables you to benefit from fast in\-memory performance for demanding applications\. DAX addresses three core scenarios:

1.  As an in\-memory cache, DAX reduces the response times of eventually\-consistent read workloads by an order of magnitude, from single\-digit milliseconds to microseconds\. 

1. DAX reduces operational and application complexity by providing a managed service that is API\-compatible with Amazon DynamoDB, and thus requires only minimal functional changes to use with an existing application\. 

1. For read\-heavy or bursty workloads, DAX provides increased throughput and potential operational cost savings by reducing the need to over\-provision read capacity units\. This is especially beneficial for applications that require repeated reads for individual keys\.

## Use Cases for DAX<a name="DAX.use-cases"></a>

DAX provides access to eventually consistent data from DynamoDB tables, with microsecond latency\. A multi\-AZ DAX cluster can serve millions of requests per second\.

DAX is ideal for:

+ Applications that require the fastest possible response time for reads\. Some examples include real\-time bidding, social gaming, and trading applications\. DAX delivers fast, in\-memory read performance for these use cases\.

+ Applications that read a small number of items more frequently than others\. For example, consider an e\-commerce system that has a one\-day sale on a popular product\. During the sale, demand for that product \(and its data in DynamoDB\) would sharply increase, compared to all of the other products\. To mitigate the impacts of a "hot" key and a non\-uniform data distribution, you could offload the read activity to a DAX cache until the one\-day sale is over\.

+ Applications that are read\-intensive, but are also cost\-sensitive\. With DynamoDB, you provision the number of reads per second that your application requires\. If read activity increases, you can increase your tables' provisioned read throughput \(at an additional cost\)\. Alternatively, you can offload the activity from your application to a DAX cluster, and reduce the amount of read capacity units you'd need to purchase otherwise\.

+ Applications that require repeated reads against a large set of data\. Such an application could potentially divert database resources from other applications\. For example, a long\-running analysis of regional weather data could temporarily consume all of the read capacity in a DynamoDB table, which would negatively impact other applications that need to access the same data\. With DAX, the weather analysis could be performed against cached data instead\.

 DAX is *not* ideal for:

+ Applications that require strongly consistent reads \(or cannot tolerate eventually consistent reads\)\.

+ Applications that do not require microsecond response times for reads, or that need to offload repeated read activity from underlying tables\.

+ Applications that are write\-intensive, or that do not perform much read activity\.

+ Applications that are already using a different caching solution with DynamoDB, and are using their own client\-side logic for working with that caching solution\.

## Usage Notes<a name="DAX.usage-notes"></a>

+ DAX is available in the following AWS regions:

  + US East \(N\. Virginia\)

  + US West \(N\. California\)

  + US West \(Oregon\)

  + EU \(Ireland\)

  + Asia Pacific \(Tokyo\)

  + Asia Pacific \(Mumbai\)

  + South America \(São Paulo\)

+ DAX supports applications written in Java or Node\.js, using the DAX clients for those programming languages\.

+ DAX does not support Transport Layer Security \(TLS\)\.

+ DAX is only available for the EC2\-VPC platform\. \(There is no support for the EC2\-Classic platform\.\)