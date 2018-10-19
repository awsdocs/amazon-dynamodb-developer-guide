# Read Consistency<a name="HowItWorks.ReadConsistency"></a>

Amazon DynamoDB is available in multiple AWS regions around the world\. Each region is independent and isolated from other AWS regions\. For example, if you have a table called *People* in the *us\-east\-2* region and another table named *People* in the *us\-west\-2* region, these are considered two entirely separate tables\. For a list of all the AWS regions in which DynamoDB is available, see [AWS Regions and Endpoints](http://docs.aws.amazon.com/general/latest/gr/rande.html#ddb_region) in the *Amazon Web Services General Reference*\.

Every AWS region consists of multiple distinct locations called Availability Zones\. Each Availability Zone is isolated from failures in other Availability Zones, and provides inexpensive, low\-latency network connectivity to other Availability Zones in the same region\. This allows rapid replication of your data among multiple Availability Zones in a region\. 

When your application writes data to a DynamoDB table and receives an HTTP 200 response \(`OK`\), all copies of the data are updated\. The data is eventually consistent across all storage locations, usually within one second or less\.

DynamoDB supports *eventually consistent* and *strongly consistent* reads\.

**Eventually Consistent Reads**  
When you read data from a DynamoDB table, the response might not reflect the results of a recently completed write operation\. The response might include some stale data\. If you repeat your read request after a short time, the response should return the latest data\.

**Strongly Consistent Reads**  
When you request a strongly consistent read, DynamoDB returns a response with the most up\-to\-date data, reflecting the updates from all prior write operations that were successful\. A strongly consistent read might not be available if there is a network delay or outage\. Consistent reads are not supported on global secondary indexes (GSI)\.

**Note**  
DynamoDB uses eventually consistent reads, unless you specify otherwise\. Read operations \(such as `GetItem`, `Query`, and `Scan`\) provide a `ConsistentRead` parameter\. If you set this parameter to true, DynamoDB uses strongly consistent reads during the operation\.
