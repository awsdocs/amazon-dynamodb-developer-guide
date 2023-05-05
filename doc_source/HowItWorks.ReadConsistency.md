# Read consistency<a name="HowItWorks.ReadConsistency"></a>

Amazon DynamoDB is available in multiple AWS Regions around the world\. Each Region is independent and isolated from other AWS Regions\. For example, if you have a table called *People* in the *us\-east\-2* Region and another table named *People* in the *us\-west\-2* Region, these are considered two entirely separate tables\. For a list of all the AWS Regions in which DynamoDB is available, see [AWS Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#ddb_region) in the *Amazon Web Services General Reference*\.

Every AWS Region consists of multiple distinct locations called Availability Zones\. Each Availability Zone is isolated from failures in other Availability Zones, and provides inexpensive, low\-latency network connectivity to other Availability Zones in the same Region\. This allows rapid replication of your data among multiple Availability Zones in a Region\.

When your application writes data to a DynamoDB table and receives an HTTP 200 response \(`OK`\), the write has occurred and is durable\. The data is eventually consistent across all storage locations, usually within one second or less\.

DynamoDB supports *eventually consistent* and *strongly consistent* reads\.

**Eventually Consistent Reads**  
When you read data from a DynamoDB table, the response might not reflect the results of a recently completed write operation\. The response might include some stale data\. If you repeat your read request after a short time, the response should return the latest data\.

**Strongly Consistent Reads**  
When you request a strongly consistent read, DynamoDB returns a response with the most up\-to\-date data, reflecting the updates from all prior write operations that were successful\. However, this consistency comes with some disadvantages:
+ Strongly consistent reads are not supported on global secondary indexes\.
+ Strongly consistent reads use more throughput capacity than eventually consistent reads\. If there is a network delay or outage, a strongly consistent read might not be available and DynamoDB may return a server error \(HTTP 500\)\. For details, see [Read/write capacity mode](HowItWorks.ReadWriteCapacityMode.md)
+ If read requests do not reach the Leader node on the first attempt, then strongly consistent reads may experience a higher latency\.

**Note**  
DynamoDB uses eventually consistent reads, unless you specify otherwise\. Read operations \(such as `GetItem`, `Query`, and `Scan`\) provide a `ConsistentRead` parameter\. If you set this parameter to true, DynamoDB uses strongly consistent reads during the operation\.