# Getting Started Developing with Python and DynamoDB<a name="GettingStarted.Python"></a>

In this tutorial, you use the AWS SDK for Python \(Boto 3\) to write simple programs to perform the following Amazon DynamoDB operations:
+ Create a table called `Movies` and load sample data in JSON format\.
+ Perform create, read, update, and delete operations on the table\.
+ Run simple queries\.

As you work through this tutorial, you can refer to the AWS SDK for Python \(Boto\) documentation\. The following sections are specific to DynamoDB:
+ [DynamoDB tutorial](http://boto3.amazonaws.com/v1/documentation/api/latest/guide/dynamodb.html)
+ [DynamoDB low\-level client](http://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/dynamodb.html)

## Tutorial Prerequisites<a name="GettingStarted.Python.Steps"></a>
+ Download and run DynamoDB on your computer\. For more information, see [Setting Up DynamoDB Local \(Downloadable Version\)](DynamoDBLocal.md)\. 
**Note**  
You use the downloadable version of DynamoDB in this tutorial\. In the [Summary](GettingStarted.Python.Summary.md), we explain how to run the same code against the DynamoDB web service\.
+ Set up an AWS access key to use the AWS SDKs\. For more information, see [Setting Up DynamoDB \(Web Service\)](SettingUp.DynamoWebService.md)\. 
+ Install Python 2\.6 or later\. For more information, see [https://www\.python\.org/downloads](https://www.python.org/downloads/)\. For instructions, see [Quickstart](http://boto3.amazonaws.com/v1/documentation/api/latest/guide/quickstart.html) in the Boto 3 documentation\.