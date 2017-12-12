# Summary<a name="GettingStarted.Java.Summary"></a>

In this tutorial, you created the `Movies` table in DynamoDB on your computer and performed basic operations\. The downloadable version of DynamoDB is useful during application development and testing\. However, when you're ready to run your application in a production environment, you must modify your code so that it uses the Amazon DynamoDB web service\.

## Modifying the Code to Use the DynamoDB Service<a name="GettingStarted.Java.Summary.MovingToDDB"></a>

To use the DynamoDB service, you must change the endpoint in your application\.

1. Remove the following import:

   ```
   import com.amazonaws.client.builder.AwsClientBuilder;
   ```

1. Next, go to `AmazonDynamoDB` in the code:

   ```
   AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard().withEndpointConfiguration(
   new AwsClientBuilder.EndpointConfiguration("http://localhost:8000", "us-west-2"))
   .build();
   ```

1. Now modify the client so that it accesses an AWS region instead of a specific endpoint:

   ```
   AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard()
   .withRegion(Regions.REGION)
   .build();
   ```

   For example, if you want to access the `us-west-2 region`, you would do this:

   ```
   AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard()
   .withRegion(Regions.US_WEST_2)
   .build();
   ```

Instead of using DynamoDB on your computer, the program now uses the Amazon DynamoDB web service endpoint in US West \(Oregon\)\.

Amazon DynamoDB is available in several regions worldwide\. For the complete list, see [Regions and Endpoints](http://docs.aws.amazon.com/general/latest/gr/rande.html) in the *AWS General Reference*\. For more information about setting regions and endpoints in your code, see [AWS Region Selection](http://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/java-dg-region-selection.html) in the *AWS SDK for Java Developer Guide*\.