# Deploying DynamoDB Locally on Your Computer<a name="DynamoDBLocal.DownloadingAndRunning"></a>

The downloadable version of Amazon DynamoDB is provided as an executable `.jar` file\. The application runs on Windows, Linux, macOS, and other platforms that support Java\.

Follow these steps to set up and run DynamoDB on your computer\.

**To set up DynamoDB on your computer**

1. Download DynamoDB for free using one of the following links\.  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DynamoDBLocal.DownloadingAndRunning.html)

   Downloadable DynamoDB is available on Apache Maven\. For more information, see [Deploying DynamoDB Local by Adding an Apache Maven Repository](DynamoDBLocal.Maven.md)\. DynamoDB is also available as part of the AWS Toolkit for Eclipse\. For more information, see [AWS Toolkit For Eclipse](https://aws.amazon.com/eclipse/)\.
**Important**  
To run DynamoDB on your computer, you must have the Java Runtime Environment \(JRE\) version 6\.x or newer\. The application doesn't run on earlier JRE versions\.

1. After you download the archive, extract the contents and copy the extracted directory to a location of your choice\.

1. To start DynamoDB on your computer, open a command prompt window, navigate to the directory where you extracted `DynamoDBLocal.jar`, and enter the following command\.

   ```
   java -Djava.library.path=./DynamoDBLocal_lib -jar DynamoDBLocal.jar -sharedDb
   ```
**Note**  
 If you're using Windows PowerShell, be sure to enclose the parameter name or the entire name and value like this:   
`java -D"java.library.path=./DynamoDBLocal_lib" -jar DynamoDBLocal.jar`  
DynamoDB processes incoming requests until you stop it\. To stop DynamoDB, press Ctrl\+C at the command prompt\.  
DynamoDB uses port 8000 by default\. If port 8000 is unavailable, this command throws an exception\. For a complete list of DynamoDB runtime options, including `-port`, enter this command\.  
`java -Djava.library.path=./DynamoDBLocal_lib -jar DynamoDBLocal.jar -help`

1. Before you can access DynamoDB programmatically or through the AWS Command Line Interface \(AWS CLI\), you must configure your credentials to enable authorization for your applications\. Downloadable DynamoDB requires any credentials to work, as shown in the following example\. 

   ```
   AWS Access Key ID: "fakeMyKeyId"
   AWS Secret Access Key: "fakeSecretAccessKey"
   ```

    You can use the `aws configure` command of the AWS CLI to set up credentials\. For more information, see [Using the AWS CLI](Tools.CLI.md)\. 

1. Start writing applications\. To access DynamoDB running locally with the AWS CLI, use the `--endpoint-url` parameter\. For example, use the following command to list DynamoDB tables\.

   ```
   aws dynamodb list-tables --endpoint-url http://localhost:8000
   ```