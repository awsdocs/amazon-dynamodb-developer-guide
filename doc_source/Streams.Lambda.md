# DynamoDB Streams and AWS Lambda Triggers<a name="Streams.Lambda"></a>

**Topics**
+ [Tutorial: Process New Items with DynamoDB Streams and Lambda](Streams.Lambda.Tutorial.md)
+ [Best Practices with Lambda](Streams.Lambda.BestPracticesWithDynamoDB.md)

Amazon DynamoDB is integrated with AWS Lambda so that you can create *triggers*—pieces of code that automatically respond to events in DynamoDB Streams\. With triggers, you can build applications that react to data modifications in DynamoDB tables\.

If you enable DynamoDB Streams on a table, you can associate the stream Amazon Resource Name \(ARN\) with an AWS Lambda function that you write\. Immediately after an item in the table is modified, a new record appears in the table's stream\. AWS Lambda polls the stream and invokes your Lambda function synchronously when it detects new stream records\. 

The Lambda function can perform any actions you specify, such as sending a notification or initiating a workflow\. For example, you can write a Lambda function to simply copy each stream record to persistent storage, such as Amazon Simple Storage Service \(Amazon S3\), to create a permanent audit trail of write activity in your table\. Or suppose that you have a mobile gaming app that writes to a `GameScores` table\. Whenever the `TopScore` attribute of the `GameScores` table is updated, a corresponding stream record is written to the table's stream\. This event could then trigger a Lambda function that posts a congratulatory message on a social media network\. \(The function would simply ignore any stream records that are not updates to `GameScores` or that do not modify the `TopScore` attribute\.\)

If your function returns an error, Lambda retries the batch until it processes successfully or the data expires\. You can also configure Lambda to retry with a smaller batch, limit the number of retries, discard records once they become too old, and other options\.

For more information about AWS Lambda, see the [AWS Lambda Developer Guide](https://docs.aws.amazon.com/lambda/latest/dg/)\.