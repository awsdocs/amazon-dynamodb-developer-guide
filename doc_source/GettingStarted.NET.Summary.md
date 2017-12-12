# Summary<a name="GettingStarted.NET.Summary"></a>

In this tutorial, you created the `Movies` table in DynamoDB on your computer and performed basic operations\. DynamoDB \(Downloadable Version\) is useful during application development and testing\. However, when you're ready to run your application in a production environment, you must modify your code so that it uses the Amazon DynamoDB service\. 

## Modifying the Code to Use the DynamoDB Service<a name="GettingStarted.NET.Summary.MovingToDDB"></a>

To use the Amazon DynamoDB service, you must change the endpoint in your application\. 

1. To do this, first remove the following line:

   ```
   ddbConfig.ServiceURL = "http://localhost:8000";
   ```

1. Add a new line that specifies the AWS Region you want to access:

   ```
   ddbConfig.RegionEndpoint = RegionEndpoint.REGION;
   ```

   For example, if you want to access the `us-west-2 region`, you would do this:

   ```
   ddbConfig.RegionEndpoint = RegionEndpoint.USWest2;
   ```

Instead of using the downloadable version of DynamoDB, the program now uses the DynamoDB service endpoint in US West \(Oregon\)\. 

Amazon DynamoDB is available in several Regions worldwide\. For the complete list, see [Regions and Endpoints](http://docs.aws.amazon.com/general/latest/gr/rande.html) in the *AWS General Reference*\. For more information about setting Regions and endpoints in your code, see [AWS Region Selection](http://docs.aws.amazon.com/sdk-for-net/latest/developer-guide/net-dg-region-selection.html) in the *AWS SDK for \.NET Developer Guide*\.