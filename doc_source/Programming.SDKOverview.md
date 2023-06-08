# Overview of AWS SDK support for DynamoDB<a name="Programming.SDKOverview"></a>

The following diagram provides a high\-level overview of Amazon DynamoDB application programming using the AWS SDKs\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/SDKSupport.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

1. You write an application using an AWS SDK for your programming language\.

1. Each AWS SDK provides one or more programmatic interfaces for working with DynamoDB\. The specific interfaces available depend on which programming language and AWS SDK you use\. Options include:
   + [Low\-level interfaces](Programming.SDKs.Interfaces.LowLevel.md)
   + [Document interfaces](Programming.SDKs.Interfaces.Document.md)
   + [Object persistence interface](Programming.SDKs.Interfaces.Mapper.md)
   + [High Level Interfaces](HigherLevelInterfaces.md)

1. The AWS SDK constructs HTTP\(S\) requests for use with the low\-level DynamoDB API\.

1. The AWS SDK sends the request to the DynamoDB endpoint\.

1. DynamoDB runs the request\. If the request is successful, DynamoDB returns an HTTP 200 response code \(OK\)\. If the request is unsuccessful, DynamoDB returns an HTTP error code and an error message\.

1. The AWS SDK processes the response and propagates it back to your application\.

Each of the AWS SDKs provides important services to your application, including the following:
+ Formatting HTTP\(S\) requests and serializing request parameters\.
+ Generating a cryptographic signature for each request\.
+ Forwarding requests to a DynamoDB endpoint and receiving responses from DynamoDB\.
+ Extracting the results from those responses\.
+ Implementing basic retry logic in case of errors\.

You do not need to write code for any of these tasks\.

**Note**  
For more information about AWS SDKs, including installation instructions and documentation, see [Tools for Amazon Web Services](https://aws.amazon.com/tools)\.