# Best Practices with Lambda<a name="Streams.Lambda.BestPracticesWithDynamoDB"></a>

An AWS Lambda function runs within a *container*—an execution environment that is isolated from other functions\. When you run a function for the first time, AWS Lambda creates a new container and begins executing the function's code\.

A Lambda function has a *handler* that is run once per invocation\. The handler contains the main business logic for the function\. For example, the Lambda function shown in [Step 4: Create and Test a Lambda Function](Streams.Lambda.Tutorial.md#Streams.Lambda.Tutorial.LambdaFunction) has a handler that can process records in a DynamoDB stream\. 

You can also provide initialization code that runs one time only—after the container is created, but before AWS Lambda runs the handler for the first time\. The Lambda function shown in [Step 4: Create and Test a Lambda Function](Streams.Lambda.Tutorial.md#Streams.Lambda.Tutorial.LambdaFunction) has initialization code that imports the SDK for JavaScript in Node\.js, and creates a client for Amazon SNS\. These objects should only be defined once, outside of the handler\.

After the function runs, AWS Lambda might opt to reuse the container for subsequent invocations of the function\. In this case, your function handler might be able to reuse the resources that you defined in your initialization code\. \(You cannot control how long AWS Lambda will retain the container, or whether the container will be reused at all\.\)

For DynamoDB triggers using AWS Lambda, we recommend the following:
+ AWS service clients should be instantiated in the initialization code, not in the handler\. This allows AWS Lambda to reuse existing connections, for the duration of the container's lifetime\.
+ In general, you do not need to explicitly manage connections or implement connection pooling because AWS Lambda manages this for you\.

For more information, see [Best Practices for Working with AWS Lambda Functions](https://docs.aws.amazon.com/lambda/latest/dg/best-practices.html) in the *AWS Lambda Developer Guide*\.