# Getting Started with Java and DynamoDB<a name="GettingStarted.Java"></a>

In this tutorial, you use the AWS SDK for Java to write simple programs to perform the following Amazon DynamoDB operations:
+ Create a table called `Movies` and load sample data in JSON format\.
+ Perform create, read, update, and delete operations on the table\.
+ Run simple queries\.

 The SDK for Java offers several programming models for different use cases\. In this exercise, the Java code uses the document model that provides a level of abstraction that makes it easier for you to work with JSON documents\.

As you work through this tutorial, you can refer to the [AWS SDK for Java Documentation](https://docs.aws.amazon.com/sdk-for-java/index.html)\.

## Tutorial Prerequisites<a name="GettingStarted.Java.Prereqs"></a>
+ Download and run DynamoDB on your computer\. For more information, see [Setting Up DynamoDB Local \(Downloadable Version\)](DynamoDBLocal.md)\. 

  DynamoDB \(downloadable version\) is also available as part of the AWS Toolkit for Eclipse\. For more information, see [AWS Toolkit For Eclipse](https://aws.amazon.com/eclipse/)\.
**Note**  
You use the downloadable version of DynamoDB in this tutorial\. For information about how to run the same code against the DynamoDB web service, see the [Summary](GettingStarted.Java.Summary.md)\.
+ Set up an AWS access key to use the AWS SDKs\. For more information, see [Setting Up DynamoDB \(Web Service\)](SettingUp.DynamoWebService.md)\. 
+ Set up the AWS SDK for Java:
  + Install a Java development environment\. If you are using the Eclipse IDE, install the AWS Toolkit for Eclipse\.
  + Install the AWS SDK for Java\.
  + Set up your AWS security credentials for use with the SDK for Java\.

  For instructions, see [Getting Started](https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/getting-started.html) in the *AWS SDK for Java Developer Guide*\.