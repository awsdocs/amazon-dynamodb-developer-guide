# Summary<a name="GettingStarted.NodeJs.Summary"></a>

In this tutorial, you created the `Movies` table in Amazon DynamoDB on your computer and performed basic operations\. The downloadable version of DynamoDB is useful during application development and testing\. However, when you're ready to run your application in a production environment, you must modify your code so that it uses the DynamoDB web service\.

## Modifying the Code to Use the DynamoDB Service<a name="GettingStarted.NodeJs.Summary.MovingToDDB"></a>

To use the DynamoDB service, you must change the endpoint in your application\. To do this, modify the following code\. 

```
AWS.config.update({endpoint: "https://dynamodb.aws-region.amazonaws.com"});
```

For example, if you want to use the us\-west\-2 Region, set the following endpoint\.

```
AWS.config.update({endpoint: "https://dynamodb.us-west-2.amazonaws.com"});
```

Instead of using DynamoDB on your computer, the program now uses the DynamoDB web service endpoint in the US West \(Oregon\) Region\. 

DynamoDB is available in AWS Regions worldwide\. For the complete list, see [Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html) in the *AWS General Reference*\. For more information about setting Regions and endpoints in your code, see [Setting the Region](https://docs.aws.amazon.com/sdk-for-javascript/v2/developer-guide/setting-region.html) in the *AWS SDK for JavaScript Developer Guide*\.