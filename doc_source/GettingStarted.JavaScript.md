# Getting Started with JavaScript and DynamoDB<a name="GettingStarted.JavaScript"></a>

In this tutorial, you use JavaScript to write simple programs to perform the following Amazon DynamoDB operations:
+ Create a table called `Movies` and load sample data in JSON format\.
+ Perform create, read, update, and delete operations on the table\.
+ Run simple queries\.

As you work through this tutorial, you can refer to the [AWS SDK for JavaScript API Reference](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/)\.

## Tutorial Prerequisites<a name="GettingStarted.Js.Prereqs"></a>
+ Download and run DynamoDB on your computer\. For more information, see [Setting Up DynamoDB Local \(Downloadable Version\)](DynamoDBLocal.md)\. 
**Note**  
You use the downloadable version of DynamoDB in this tutorial\. For information about how to run the same code against the DynamoDB web service, see the [Summary and Review of JavaScript and DynamoDB Tutorial](GettingStarted.Js.Summary.md)\. 
+ Set up an AWS access key to use AWS SDKs\. For more information, see [Setting Up DynamoDB \(Web Service\)](SettingUp.DynamoWebService.md)\. 
+ Set up the AWS SDK for JavaScript\. To do this, add or modify the following script tag to your HTML pages:

  ```
  <script src="https://sdk.amazonaws.com/js/aws-sdk-2.7.16.min.js"></script>
  ```
**Note**  
 The version of AWS SDK for JavaScript might have been updated\. For the latest version, see the [AWS SDK for JavaScript API Reference](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/)\. 
+ Enable [cross\-origin resource sharing \(CORS\)](https://docs.aws.amazon.com/sdk-for-javascript/v2/developer-guide/cors.html) so that your computer's browser can communicate with the downloadable version of DynamoDB\.

**To enable CORS**

  1.  Download the free ModHeader Chrome browser extension \(or any other browser extension that allows you to modify HTTP response headers\)\. 

  1. Run the ModHeader Chrome browser extension, and add an HTTP response header with the name set to "Access\-Control\-Allow\-Origin" and a value of "null" or "\*"\.
**Important**  
This configuration is required only while running this tutorial program for JavaScript on your computer\. After you finish the tutorial, you should disable or remove this configuration\.

  1. You can now run the JavaScript tutorial program files\.

 If you prefer to run a complete version of the JavaScript tutorial program instead of performing step\-by\-step instructions, do the following: 

1. Download the following file: [MoviesJavaScript\.zip](samples/MoviesJavaScript.zip)\. 

1. Extract the file `MoviesJavaScript.html` from the archive\. 

1. Modify the `MoviesJavaScript.html` file to use your endpoint\. 

1. Run the `MoviesJavaScript.html` file\. 