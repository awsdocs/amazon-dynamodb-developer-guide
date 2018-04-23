# Tagging for DynamoDB<a name="Tagging"></a>

 You can label DynamoDB resources with tags\. Tags allow you to categorize your resources in different ways, for example, by purpose, owner, environment, or other criteria\. Tags can help you: 
+  Quickly identify a resource based on the tags you've assigned to it\. 
+  See AWS bills broken down by tags\. 
**Note**  
 Any Local Secondary Indexes \(LSI\) and Global Secondary Indexes \(GSI\) related to tagged tables are labeled with the same tags automatically\. Currently, DynamoDB Streams usage cannot be tagged\. 

 Tagging is supported by AWS services like Amazon EC2, Amazon S3, DynamoDB, and more\. Efficient tagging can provide cost insights by allowing you to create reports across services that carry a specific tag\. 

 To get started with tagging, you should: 

1.  Understand [Tagging Restrictions](TaggingRestrictions.md)\. 

1.  Create tags by using [Tagging Operations](Tagging.Operations.md)\. 

1.  Use [Cost Allocation Reports](CostAllocationReports.md) to track your AWS costs per active tag\. 

 Finally, it is good practice to follow optimal tagging strategies\. For information, see [AWS Tagging Strategies](https://d0.awsstatic.com/aws-answers/AWS_Tagging_Strategies.pdf)\. 