# \.NET and DynamoDB Tutorial Prerequisites<a name="GettingStarted.NET.Prereqs"></a>

The [Microsoft \.NET and DynamoDB Tutorial](GettingStarted.NET.md) describes how to use the AWS SDK for \.NET to create simple programs for performing Amazon DynamoDB operations\. 

Before you begin, follow these steps to ensure that you have all the prerequisites needed to complete the tutorial:
+ Use a computer that is running a recent version of Windows and a current version of Microsoft Visual Studio\. If you don't have Visual Studio installed, you can download a free copy of the Community edition from the [Microsoft Visual Studio website](https://www.visualstudio.com)\.
+ Download and run DynamoDB \(downloadable version\)\. For more information, see [Setting Up DynamoDB Local \(Downloadable Version\)](DynamoDBLocal.md)\.
**Note**  
You use the downloadable version of DynamoDB in this tutorial\. For more information about how to run the same code against the DynamoDB web service, see [Step 1: Create a DynamoDB Client](GettingStarted.NET.01.md)\.
+ Set up an AWS access key to use the AWS SDKs\. For more information, see [Setting Up DynamoDB \(Web Service\)](SettingUp.DynamoWebService.md)\.
+ Set up a security profile for DynamoDB in Visual Studio\. For step\-by\-step instructions, see [\.NET Code Examples](CodeSamples.DotNet.md)\.
+ Open the getting started demo solution that is used in this tutorial in Visual Studio:

  1. Download a \.zip archive containing the solution from [DynamoDB\_intro\.zip](samples/DynamoDB_intro.zip)\.

  1. Save the archive to a convenient place on your computer, and extract \(unzip\) the files in it\.

  1. In Visual Studio, press **Ctrl\+Shift\+O**, or choose **Open** on the **File** menu and choose **Project/Solution**\.

  1. Navigate to `DynamoDB_intro.sln` in the `DynamoDB_intro` directory that you unzipped, and choose **Open**\.
+ Build the `DynamoDB_intro` solution, and then open the `00_Main.cs` file in Visual Studio\.

  1. On the **Build** menu, choose **Build Solution** \(or press `Ctrl+Shift+B`\)\. The solution should build successfully\.

  1. Make sure that the **Solution Explorer** pane is being displayed and pinned in Visual Studio\. If it isn't, you can find it in the **View** menu, or by pressing **Ctrl\+Alt\+L**\.

  1. In **Solution Explorer**, open the **00\_Main\.cs** file\. This is the file that controls the execution of the demo program that is used in this tutorial\.

**Note**  
This tutorial shows how to use asynchronous methods rather than synchronous methods\. This is because \.NET core supports only asynchronous methods, and also because the asynchronous model is generally preferable when performance is crucial\. For more information, see [AWS Asynchronous APIs for \.NET](https://docs.aws.amazon.com/sdk-for-net/v3/developer-guide/sdk-net-async-api.html)\. 

## Installing the External Dependencies of the DynamoDB\_intro Solution<a name="GettingStarted.NET.Prereqs.Dependencies"></a>

The `DynamoDB_intro` solution already has the Amazon DynamoDB SDK installed in it\. It also has the open\-source Newtonsoft `Json.NET` library for deserializing JSON data, licensed under the MIT License \(MIT\) \(see [https://github\.com/JamesNK/Newtonsoft\.Json/blob/master/LICENSE\.md](https://github.com/JamesNK/Newtonsoft.Json/blob/master/LICENSE.md)\)\.

To install the NuGet package for the DynamoDB module of the AWS SDK for \.NET version 3 in your own programs, open the **NuGet Package Manager Console** on the **Tools** menu in Visual Studio\. Then enter the following command at the `PM>` prompt\.

```
PM> Install-Package AWSSDK.DynamoDBv2
```

In a similar way, you can use the **NuGet Package Manager Console** to load the `Json.NET` library into your own projects in Visual Studio\. At the `PM>` prompt, enter the following command\.

```
PM> Install-Package Newtonsoft.Json
```

## Next Step<a name="GettingStarted.NET.Prereqs.NextStep"></a>

[Step 1: Create a DynamoDB Client](GettingStarted.NET.01.md)