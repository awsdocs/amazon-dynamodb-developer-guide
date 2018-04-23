# DynamoDB Streams and AWS Lambda Triggers<a name="Streams.Lambda"></a>

**Topics**
+ [Tutorial: Processing New Items in a DynamoDB Table](Streams.Lambda.Tutorial.md)
+ [Best Practices](Streams.Lambda.BestPracticesWithDynamoDB.md)

Amazon DynamoDB is integrated with AWS Lambda so that you can create *triggers*—pieces of code that automatically respond to events in DynamoDB Streams\. With triggers, you can build applications that react to data modifications in DynamoDB tables\.

If you enable DynamoDB Streams on a table, you can associate the stream ARN with a Lambda function that you write\. Immediately after an item in the table is modified, a new record appears in the table's stream\. AWS Lambda polls the stream and invokes your Lambda function synchronously when it detects new stream records\. 

The Lambda function can perform any actions you specify, such as sending a notification or initiating a workflow\. For example, you can write a Lambda function to simply copy each stream record to persistent storage, such as Amazon Simple Storage Service \(Amazon S3\), to create a permanent audit trail of write activity in your table\. Or suppose you have a mobile gaming app that writes to a `GameScores` table\. Whenever the `TopScore` attribute of the `GameScores` table is updated, a corresponding stream record is written to the table's stream\. This event could then trigger a Lambda function that posts a congratulatory message on a social media network\. \(The function would simply ignore any stream records that are not updates to `GameScores` or that do not modify the `TopScore` attribute\.\)

For more information about AWS Lambda, see the [AWS Lambda Developer Guide](http://docs.aws.amazon.com/lambda/latest/dg/)\.