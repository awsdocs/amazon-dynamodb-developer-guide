# Summary<a name="GettingStarted.Python.Summary"></a>

In this tutorial, you created the `Movies` table in the downloadable version of DynamoDB on your computer and performed basic operations\. The downloadable version of DynamoDB is useful during application development and testing\. However, when you're ready to run your application in a production environment, you must modify your code so that it uses the Amazon DynamoDB web service\.

## Modifying the Code to Use the DynamoDB Service<a name="GettingStarted.Python.Summary.MovingToDDB"></a>

To use the Amazon DynamoDB service, you must change the endpoint in your application\. To do this, modify the following line:

```
dynamodb = boto3.resource('dynamodb',endpoint_url="http://localhost:8000") 
```

For example, if you want to use the `us-west-2` Region:

```
dynamodb = boto3.resource('dynamodb',region_name='us-west-2') 
```

Instead of using the downloadable version of DynamoDB on your computer, the program now uses the DynamoDB service in US West \(Oregon\)\.

DynamoDB is available in several Regions worldwide\. For the complete list, see [Regions and Endpoints](http://docs.aws.amazon.com/general/latest/gr/rande.html) in the *AWS General Reference*\. For more information about setting Regions and endpoints in your code, see [AWS Region Selection](http://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/java-dg-region-selection.html) in the *AWS SDK for Java Developer Guide*\.