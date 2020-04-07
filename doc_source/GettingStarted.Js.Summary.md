# Summary and Review of JavaScript and DynamoDB Tutorial<a name="GettingStarted.Js.Summary"></a>

In this tutorial, you created the `Movies` table in Amazon DynamoDB on your computer and performed basic operations\. The downloadable version of DynamoDB is useful during application development and testing\. However, when you're ready to run your application in a production environment, you must modify your code so that it uses the DynamoDB web service\. 

## Updating the AWS Configuration Region to Use the DynamoDB Web Service<a name="GettingStarted.Js.Summary.MovingToDDB"></a>

To use the DynamoDB web service, you must update the AWS Region in your application\. You also have to make sure that Amazon Cognito is available in that same Region so that your browser scripts can authenticate successfully\.

```
AWS.config.update({region: "aws-region"});
```

For example, if you want to use the us\-west\-2 Region, set the following Region\.

```
AWS.config.update({region: "us-west-2"});
```

The program now uses the Amazon DynamoDB web service in the US West \(Oregon\) Region\.

DynamoDB is available in AWS Regions worldwide\. For the complete list, see [Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html) in the *AWS General Reference*\. For more information about setting Regions and endpoints in your code, see [Setting the Region](https://docs.aws.amazon.com/sdk-for-javascript/v2/developer-guide/setting-region.html) in the *AWS SDK for JavaScript Getting Started Guide*\.

## Configuring AWS Credentials in Your Files Using Amazon Cognito<a name="GettingStarted.Js.Conf.Credentials"></a>

 The recommended way to obtain AWS credentials for your web and mobile applications is to use Amazon Cognito\. Amazon Cognito helps you avoid hardcoding your AWS credentials in your files\. Amazon Cognito uses AWS Identity and Access Management \(IAM\) roles to generate temporary credentials for your application's authenticated and unauthenticated users\.

 For more information, see [Configuring AWS Credentials in Your Files Using Amazon Cognito](Cognito.Credentials.md)\.