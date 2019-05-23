# Summary<a name="GettingStarted.Ruby.Summary"></a>

In this tutorial, you created the `Movies` table in the downloadable version of DynamoDB on your computer and performed basic operations\. The downloadable version of DynamoDB is useful during application development and testing\. However, when you're ready to run your application in a production environment, you must modify your code so that it uses the Amazon DynamoDB web service\.

## Modifying the Code to Use the DynamoDB Service<a name="GettingStarted.Ruby.Summary.MovingToDDB"></a>

To use the Amazon DynamoDB service, you must change the endpoint in your application in order\. To do this, find the following lines in the code:

```
Aws.config.update({
region: "us-west-2",
endpoint: "http://localhost:8000"
})
```

Remove the `endpoint` parameter so that the code looks like this:

```
Aws.config.update({
region: "us-west-2"
]);
```

After you remove this line, the code can access the DynamoDB service in the Region specified by the `region` config value\. 

Instead of using the version of DynamoDB on your computer, the program uses the DynamoDB service endpoint in US West \(Oregon\)\.

DynamoDB is available in several Regions worldwide\. For the complete list, see [Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html) in the *AWS General Reference*\. For more information, see the [AWS SDK for Ruby Getting Started Guide](https://aws.amazon.com/developers/getting-started/ruby)\.