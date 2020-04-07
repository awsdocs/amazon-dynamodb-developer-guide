# Getting Started with Node\.js and DynamoDB<a name="GettingStarted.NodeJs"></a>

In this tutorial, you use the AWS SDK for JavaScript to write simple applications to perform the following Amazon DynamoDB operations:
+ Create a table named `Movies` and load sample data in JSON format\.
+ Perform create, read, update, and delete operations on the table\.
+ Run simple queries\.

As you work through this tutorial, you can refer to the [AWS SDK for JavaScript API Reference](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/)\.

## Tutorial Prerequisites<a name="GettingStarted.NodeJs.Prereqs"></a>
+ Download and run DynamoDB on your computer\. For more information, see [Setting Up DynamoDB Local \(Downloadable Version\)](DynamoDBLocal.md)\. 
**Note**  
You use the downloadable version of DynamoDB in this tutorial\. For information about how to run the same code against the DynamoDB web service, see the [Summary](GettingStarted.NodeJs.Summary.md)\. 
+ Set up an AWS access key to use the AWS SDKs\. For more information, see [Setting Up DynamoDB \(Web Service\)](SettingUp.DynamoWebService.md)\. 
+ Set up the AWS SDK for JavaScript:
  + Install [Node\.js](http://nodejs.org)\.
  + Install the [AWS SDK for JavaScript](https://aws.amazon.com/sdk-for-node-js)\.

  For more information, see the [AWS SDK for JavaScript Getting Started Guide](https://docs.aws.amazon.com/AWSJavaScriptSDK/guide/browser-intro.html)\.