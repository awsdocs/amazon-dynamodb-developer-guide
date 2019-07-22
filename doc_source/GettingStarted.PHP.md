# PHP and DynamoDB<a name="GettingStarted.PHP"></a>

In this tutorial, you use the AWS SDK for PHP to write simple programs to perform the following Amazon DynamoDB operations:
+ Create a table called `Movies` and load sample data in JSON format\.
+ Perform create, read, update, and delete operations on the table\.
+ Run simple queries\.

As you work through this tutorial, you can refer to the [AWS SDK for PHP Developer Guide](https://docs.aws.amazon.com/aws-sdk-php/v3/guide/)\. The [Amazon DynamoDB section](https://docs.aws.amazon.com/aws-sdk-php/v3/api/api-dynamodb-2012-08-10.html) in the *AWS SDK for PHP API Reference* describes the parameters and results for DynamoDB operations\.

## Tutorial Prerequisites<a name="GettingStarted.PHP.Prereqs"></a>
+ Download and run DynamoDB on your computer\. For more information, see [Setting Up DynamoDB Local \(Downloadable Version\)](DynamoDBLocal.md)\. 
**Note**  
You use the downloadable version of DynamoDB in this tutorial\. For information about how to run the same code against the DynamoDB service, see the [Summary](GettingStarted.PHP.Summary.md)\.
+ Set up an AWS access key to use the AWS SDKs\. For more information, see [Setting Up DynamoDB \(Web Service\)](SettingUp.DynamoWebService.md)\. 
+ Set up the AWS SDK for PHP:
  + [Install PHP](http://php.net)\.
  + [Install the SDK for PHP](https://aws.amazon.com/sdk-for-php/)\.

  For more information, see [Getting Started](https://docs.aws.amazon.com/aws-sdk-php/v3/guide/getting-started/) in the *AWS SDK for PHP Getting Started Guide*\.