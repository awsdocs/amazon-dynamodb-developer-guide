# Summary<a name="GettingStarted.PHP.Summary"></a>

In this tutorial, you created the `Movies` table in Amazon DynamoDB on your computer and performed basic operations\. The downloadable version of DynamoDB is useful during application development and testing\. However, when you're ready to run your application in a production environment, you need to modify your code so that it uses the DynamoDB web service\. 

## Modifying the Code to Use the DynamoDB Service<a name="GettingStarted.Summary.MovingToDDB"></a>

To use the DynamoDB web service, you must change the endpoint in your application\. To do this, find the following lines in the code\.

```
$sdk = new Aws\Sdk([
    'endpoint'   => 'http://localhost:8000',
    'region'   => 'us-west-2',
    'version'  => 'latest'
]);
```

Remove the `endpoint` parameter so that the code looks like the following\.

```
$sdk = new Aws\Sdk([
    'region'   => 'us-west-2',
    'version'  => 'latest'
]);
```

After you remove this line, the code can access the DynamoDB web service in the AWS Region specified by the `region` config value\. For example, the following line specifies that you want to use the US West \(Oregon\) Region\.

```
'region'   => 'us-west-2',
```

Instead of using the downloadable version of DynamoDB on your computer, the program now uses the DynamoDB service endpoint in the US West \(Oregon\) Region\.

DynamoDB is available in AWS Regions worldwide\. For the complete list, see [Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html) in the *AWS General Reference*\. For more information about setting Regions and endpoints in your code, see the [boto: A Python interface to Amazon Web Services](http://boto.readthedocs.org/en/latest/)\.