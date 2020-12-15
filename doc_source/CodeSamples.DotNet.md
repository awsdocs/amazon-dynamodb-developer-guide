# \.NET Code Examples<a name="CodeSamples.DotNet"></a>

**Topics**
+ [\.NET: Setting Your AWS Credentials](#CodeSamples.DotNet.Credentials)
+ [\.NET: Setting the AWS Region and Endpoint](#CodeSamples.DotNet.RegionAndEndpoint)

This guide contains \.NET code snippets and ready\-to\-run programs\. You can find these code examples in the following sections:
+ [Working with Items and Attributes](WorkingWithItems.md)
+ [Working with Tables and Data in DynamoDB](WorkingWithTables.md)
+ [Working with Queries in DynamoDB](Query.md)
+ [Working with Scans in DynamoDB](Scan.md)
+ [Improving Data Access with Secondary Indexes](SecondaryIndexes.md)
+ [\.NET: Document Model](DotNetSDKMidLevel.md)
+ [\.NET: Object Persistence Model](DotNetSDKHighLevel.md)
+ [Change Data Capture for DynamoDB Streams](Streams.md)

You can get started quickly by using the AWS SDK for \.NET with the Toolkit for Visual Studio\.

**To run the \.NET code examples \(using Visual Studio\)**

1. Download and install [Microsoft Visual Studio](https://www.visualstudio.com)\.

1. Download and install the [Toolkit for Visual Studio](https://aws.amazon.com/visualstudio/)\.

1. Start Visual Studio\. Choose **File**, **New**, **Project**\.

1. In **New Project**, choose **AWS Empty Project**, and then choose **OK**\.

1. In **AWS Access Credentials**, choose **Use existing profile**, choose your credentials profile from the list, and then choose **OK**\.

   If this is your first time using Toolkit for Visual Studio, choose **Use a new profile** to set up your AWS credentials\.

1. In your Visual Studio project, choose the tab for your program's source code \(`Program.cs`\)\. Copy the code example from the documentation page into the Visual Studio editor, replacing any other code that you see in the editor\.

1. If you see error messages of the form The type or namespace name\.\.\.could not be found, you need to install the AWS SDK assembly for DynamoDB as follows:

   1. In Solution Explorer, open the context \(right\-click\) menu for your project, and then choose **Manage NuGet Packages**\.

   1. In NuGet Package Manager, choose **Browse**\.

   1. In the search box, enter **AWSSDK\.DynamoDBv2**, and wait for the search to complete\.

   1. Choose **AWSSDK\.DynamoDBv2**, and then choose **Install**\.

   1. When the installation is complete, choose the **Program\.cs** tab to return to your program\.

1. To run the code, choose **Start** in the Visual Studio toolbar\.

The AWS SDK for \.NET provides thread\-safe clients for working with DynamoDB\. As a best practice, your applications should create one client and reuse the client between threads\.

For more information, see [AWS SDK for \.NET](https://aws.amazon.com/sdk-for-net)\.

**Note**  
The code examples in this guide are intended for use with the latest version of the AWS SDK for \.NET\.

## \.NET: Setting Your AWS Credentials<a name="CodeSamples.DotNet.Credentials"></a>

The AWS SDK for \.NET requires that you provide AWS credentials to your application at runtime\. The code examples in this guide assume that you are using the SDK Store to manage your AWS credentials file, as described in [Using the SDK Store](https://docs.aws.amazon.com/sdk-for-net/v3/developer-guide/net-dg-config-creds.html#sdk-store) in the *AWS SDK for \.NET Developer Guide*\.

The Toolkit for Visual Studio supports multiple sets of credentials from any number of accounts\. Each set is referred to as a *profile*\. Visual Studio adds entries to the project's `App.config` file so that your application can find the AWS credentials at runtime\.

The following example shows the default `App.config` file that is generated when you create a new project using Toolkit for Visual Studio\.

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appSettings>
    <add key="AWSProfileName" value="default"/>
    <add key="AWSRegion" value="us-west-2" />
 </appSettings>
</configuration>
```

At runtime, the program uses the `default` set of AWS credentials, as specified by the `AWSProfileName` entry\. The AWS credentials themselves are kept in the SDK Store in encrypted form\. The Toolkit for Visual Studio provides a graphical user interface for managing your credentials, all from within Visual Studio\. For more information, see [Specifying Credentials](https://docs.aws.amazon.com/AWSToolkitVS/latest/UserGuide/tkv_setup.html#creds) in the *AWS Toolkit for Visual Studio User Guide*\.

**Note**  
By default, the code examples access DynamoDB in the US West \(Oregon\) Region\. You can change the Region by modifying the `AWSRegion` entry in the App\.config file\. You can set `AWSRegion` to any Region where DynamoDB is available\. For a complete list, see [AWS Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#ddb_region) in the *Amazon Web Services General Reference*\.

## \.NET: Setting the AWS Region and Endpoint<a name="CodeSamples.DotNet.RegionAndEndpoint"></a>

By default, the code examples access DynamoDB in the US West \(Oregon\) Region\. You can change the Region by modifying the `AWSRegion` entry in the `App.config` file\. Or, you can change the Region by modifying the `AmazonDynamoDBClient` properties\.

The following code example instantiates a new `AmazonDynamoDBClient`\. The client is modified so that the code runs against DynamoDB in a different Region\.

```
AmazonDynamoDBConfig clientConfig = new AmazonDynamoDBConfig();
// This client will access the US East 1 region.
clientConfig.RegionEndpoint = RegionEndpoint.USEast1;
AmazonDynamoDBClient client = new AmazonDynamoDBClient(clientConfig);
```

For a complete list of Regions, see [AWS Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#ddb_region) in the *Amazon Web Services General Reference*\.

If you want to run the code examples using DynamoDB locally on your computer, set the endpoint as follows\.

```
AmazonDynamoDBConfig clientConfig = new AmazonDynamoDBConfig();
// Set the endpoint URL
clientConfig.ServiceURL = "http://localhost:8000";
AmazonDynamoDBClient client = new AmazonDynamoDBClient(clientConfig);
```