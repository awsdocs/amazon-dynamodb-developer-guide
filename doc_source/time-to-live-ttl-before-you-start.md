# Before You Begin Using Time To Live<a name="time-to-live-ttl-before-you-start"></a>

 Before you enable Time To Live on a table, consider the following: 

+ Ensure that any existing timestamp values in the specified Time To Live attribute are correct and in the right format\. 

+ Items with an expiration time greater than 5 years in the past are not deleted\.

+ If data recovery is a concern, we recommend that you back up your table\.

  + For a 24\-hour recovery window, you can use Amazon DynamoDB Streams\. For more information, see [DynamoDB Streams and Time To Live](time-to-live-ttl-streams.md)\.

  + For a full backup, you can use AWS Data Pipeline\. For more information, see [Exporting and Importing DynamoDB Data Using AWS Data Pipeline](DynamoDBPipeline.md)\.

+ You can use IAM policies to prevent unauthorized updates to the TTL attribute or configuration of the Time To Live feature\. If you only allow access to specified actions in your existing IAM policies, ensure that your policies are updated to allow `dynamodb:UpdateTimeToLive` for roles that need to enable or disable Time To Live on tables\. For more information, see [Using Identity\-Based Policies \(IAM Policies\) for Amazon DynamoDB](using-identity-based-policies.md)\.

+ Consider whether you need to do any post\-processing of deleted items\. The Streams records of TTL deletes are marked and you can monitor them using an AWS Lambda function\. For more information on the additions to the Streams record, see [DynamoDB Streams and Time To Live](time-to-live-ttl-streams.md)\.