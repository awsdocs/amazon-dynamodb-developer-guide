# Use API Gateway to invoke a Lambda function<a name="example_cross_LambdaAPIGateway_section"></a>

The following code examples show how to create an AWS Lambda function invoked by Amazon API Gateway\.

**Note**  
The source code for these examples is in the [AWS Code Examples GitHub repository](https://github.com/awsdocs/aws-doc-sdk-examples)\. Have feedback on a code example? [Create an Issue](https://github.com/awsdocs/aws-doc-sdk-examples/issues/new/choose) in the code examples repo\. 

------
#### [ Java ]

**SDK for Java 2\.x**  
 Shows how to create an AWS Lambda function by using the Lambda Java runtime API\. This example invokes different AWS services to perform a specific use case\. This example demonstrates how to create a Lambda function invoked by Amazon API Gateway that scans an Amazon DynamoDB table for work anniversaries and uses Amazon Simple Notification Service \(Amazon SNS\) to send a text message to your employees that congratulates them at their one year anniversary date\.   
 For complete source code and instructions on how to set up and run, see the full example on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javav2/usecases/creating_lambda_apigateway)\.   

**Services used in this example**
+ API Gateway
+ DynamoDB
+ Lambda
+ Amazon SNS

------
#### [ JavaScript ]

**SDK for JavaScript V3**  
 Shows how to create an AWS Lambda function by using the Lambda JavaScript runtime API\. This example invokes different AWS services to perform a specific use case\. This example demonstrates how to create a Lambda function invoked by Amazon API Gateway that scans an Amazon DynamoDB table for work anniversaries and uses Amazon Simple Notification Service \(Amazon SNS\) to send a text message to your employees that congratulates them at their one year anniversary date\.   
 For complete source code and instructions on how to set up and run, see the full example on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javascriptv3/example_code/cross-services/lambda-api-gateway)\.   
This example is also available in the [AWS SDK for JavaScript v3 developer guide](https://docs.aws.amazon.com/sdk-for-javascript/v3/developer-guide/api-gateway-invoking-lambda-example.html)\.  

**Services used in this example**
+ API Gateway
+ DynamoDB
+ Lambda
+ Amazon SNS

------

For a complete list of AWS SDK developer guides and code examples, see [Using DynamoDB with an AWS SDK](sdk-general-information-section.md)\. This topic also includes information about getting started and details about previous SDK versions\.