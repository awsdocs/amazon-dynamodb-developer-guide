# \.NET and DynamoDB<a name="GettingStarted.NET"></a>

In this tutorial, you use the AWS SDK for \.NET to write simple programs to perform the following Amazon DynamoDB operations:
+ Create a table called `Movies` using a utility program written in C\#, and load sample data in JSON format\.
+ Perform create, read, update, and delete operations on the table\.
+ Run simple queries\.

 The DynamoDB module of the AWS SDK for \.NET offers several programming models for different use cases\. In this exercise, the C\# code uses both the document model, which provides a level of abstraction that is often convenient, and the low\-level API, which handles nested attributes more effectively\. For information about the document model API, see [\.NET: Document Model](DotNetSDKMidLevel.md)\. For information about the low\-level API, see [Working with Tables: \.NET](LowLevelDotNetWorkingWithTables.md)\. 

## Tutorial Prerequisites<a name="GettingStarted.NET.Prereqs"></a>
+ Use a computer that is running a recent version of Windows and a current version of Microsoft Visual Studio\. If you don't have Visual Studio installed, you can download a free copy of the Community edition from the [Microsoft Visual Studio website](https://www.visualstudio.com)\.
+ Download and run DynamoDB \(Downloadable Version\)\. For more information, see [Setting Up DynamoDB Local \(Downloadable Version\)](DynamoDBLocal.md)\.
**Note**  
You use the downloadable version of DynamoDB in this tutorial\. For more information about how to run the same code against the DynamoDB web service, see the [Summary](GettingStarted.NET.Summary.md)\. 
+ Set up an AWS access key to use the AWS SDKs\. For more information, see [Setting Up DynamoDB \(Web Service\)](SettingUp.DynamoWebService.md)\. 
+ Set up a security profile for DynamoDB in Visual Studio\. For step\-by\-step instructions, see [\.NET Code Samples](CodeSamples.DotNet.md)\. 
+ In Visual Studio, create a new project called `DynamoDB_intro` using the **Console Application** template in the **Installed/Templates/Visual C\#/** node\. This is the project you use throughout this tutorial\.
**Note**  
The following tutorial does not work with \.NET core because it does not support synchronous methods\. For more information, see [AWS Asynchronous APIs for \.NET](http://docs.aws.amazon.com/sdk-for-net/v3/developer-guide/sdk-net-async-api.html)\. 
+ Install the NuGet package for the DynamoDB module of the AWS SDK for \.NET, version 3, into your `DynamoDB_intro` project\. To do this, in Visual Studio, open the **NuGet Package Manager Console** from the **Tools** menu\. Then type the following command at the `PM>` prompt.

  ```
  PM> Install-Package AWSSDK.DynamoDBv2
  ```
