# Java Code Examples<a name="CodeSamples.Java"></a>

**Topics**
+ [Java: Setting Your AWS Credentials](#CodeSamples.Java.Credentials)
+ [Java: Setting the AWS Region and Endpoint](#CodeSamples.Java.RegionAndEndpoint)

This Developer Guide contains Java code snippets and ready\-to\-run programs\. You can find these code examples in the following sections:
+ [Working with Items in DynamoDB](WorkingWithItems.md)
+ [Working with Tables in DynamoDB](WorkingWithTables.md)
+ [Working with Queries](Query.md)
+ [Working with Scans](Scan.md)
+ [Improving Data Access with Secondary Indexes](SecondaryIndexes.md)
+ [Java: DynamoDBMapper](DynamoDBMapper.md)
+ [Capturing Table Activity with DynamoDB Streams](Streams.md)

You can get started quickly by using Eclipse with the [AWS Toolkit for Eclipse](https://aws.amazon.com/eclipse/)\. In addition to a full\-featured IDE, you also get the AWS SDK for Java with automatic updates, and preconfigured templates for building AWS applications\.

**To run the Java code examples \(using Eclipse\)**

1. Download and install the [Eclipse](http://www.eclipse.org) IDE\.

1. Download and install the [AWS Toolkit for Eclipse](https://aws.amazon.com/eclipse/)\.

1. Start Eclipse, and on the **Eclipse** menu, choose **File**, **New**, and then **Other**\.

1. In **Select a wizard**, choose **AWS**, choose **AWS Java Project**, and then choose **Next**\.

1. In **Create an AWS Java Project**, do the following:

   1. In **Project name**, enter a name for your project\.

   1. In **Select Account**, choose your credentials profile from the list\.

      If this is your first time using the [AWS Toolkit for Eclipse](https://aws.amazon.com/eclipse/), choose **Configure AWS Accounts** to set up your AWS credentials\.

1. Choose **Finish** to create the project\.

1. From the **Eclipse** menu, choose **File**, **New**, and then **Class**\.

1. In **Java Class**, enter a name for your class in **Name** \(use the same name as the code example that you want to run\), and then choose **Finish** to create the class\.

1. Copy the code example from the documentation page into the Eclipse editor\.

1. To run the code, choose **Run** on the Eclipse menu\.

The SDK for Java provides thread\-safe clients for working with DynamoDB\. As a best practice, your applications should create one client and reuse the client between threads\.

For more information, see the [AWS SDK for Java](https://aws.amazon.com/sdk-for-java)\.

**Note**  
The code examples in this guide are intended for use with the latest version of the AWS SDK for Java\.  
If you are using the AWS Toolkit for Eclipse, you can configure automatic updates for the SDK for Java\. To do this in Eclipse, go to **Preferences** and choose **AWS Toolkit**, **AWS SDK for Java**, **Download new SDKs automatically**\.

## Java: Setting Your AWS Credentials<a name="CodeSamples.Java.Credentials"></a>

The SDK for Java requires that you provide AWS credentials to your application at runtime\. The code examples in this guide assume that you are using an AWS credentials file, as described in [Set up AWS Credentials and Region for Development](https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/set-up-creds.html) in the *AWS SDK for Java Developer Guide*\.

The following is an example of an AWS credentials file named `~/.aws/credentials`, where the tilde character \(`~`\) represents your home directory\.

```
[default] 
aws_access_key_id = AWS access key ID goes here 
aws_secret_access_key = Secret key goes here
```

## Java: Setting the AWS Region and Endpoint<a name="CodeSamples.Java.RegionAndEndpoint"></a>

By default, the code examples access DynamoDB in the US West \(Oregon\) Region\. You can change the Region by modifying the `AmazonDynamoDB` properties\.

The following code example instantiates a new `AmazonDynamoDB`\.

```
import com.amazonaws.services.dynamodbv2.AmazonDynamoDBClientBuilder;
import com.amazonaws.regions.Regions;
...
// This client will default to US West (Oregon)
AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard()
.withRegion(Regions.US_WEST_2)
.build();
```

You can use the `withRegion` method to run your code against DynamoDB in any Region where it is available\. For a complete list, see [AWS Service Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#ddb_region) in the *Amazon Web Services General Reference*\.

If you want to run the code examples using DynamoDB locally on your computer, set the endpoint as follows\.

```
AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard().withEndpointConfiguration(
new AwsClientBuilder.EndpointConfiguration("http://localhost:8000", "us-west-2"))
.build();
```
