# Setting Up DynamoDB Local \(Downloadable Version\)<a name="DynamoDBLocal"></a>

The downloadable version of DynamoDB lets you write and test applications without accessing the DynamoDB web service\. Instead, the database is self\-contained on your computer\. When you're ready to deploy your application in production, you can make a few minor changes to the code so that it uses the DynamoDB web service\.

Having this local version helps you save on provisioned throughput, data storage, and data transfer fees\. In addition, you don't need an Internet connection while you're developing your application\.

**Topics**
+ [Downloading and Running DynamoDB on Your Computer](#DynamoDBLocal.DownloadingAndRunning)
+ [Setting the Local Endpoint](DynamoDBLocal.Endpoint.md)
+ [Usage Notes](DynamoDBLocal.UsageNotes.md)

## Downloading and Running DynamoDB on Your Computer<a name="DynamoDBLocal.DownloadingAndRunning"></a>

The downloadable version of DynamoDB is provided as an executable `.jar` file\. The application runs on Windows, Linux, macOS X, and other platforms that support Java\.

Follow these steps to set up and run DynamoDB on your computer:

1. Download DynamoDB for free using one of the following links:  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DynamoDBLocal.html)

   Downloadable DynamoDB is available on Apache Maven\. For more information, see [DynamoDB \(Downloadable Version\) and Apache Maven](#DynamoDBLocal.Maven), later in this topic\. DynamoDB is also available as part of the AWS Toolkit for Eclipse\. For more information, see [AWS Toolkit For Eclipse](https://aws.amazon.com/eclipse/)\.
**Important**  
To run DynamoDB on your computer, you must have the Java Runtime Environment \(JRE\) version 6\.x or newer\. The application doesn't run on older JRE versions\.

1. After you download the archive, extract the contents and copy the extracted directory to a location of your choice\.

1. To start DynamoDB on your computer, open a command prompt window, navigate to the directory where you extracted `DynamoDBLocal.jar`, and type the following command:

   ```
   java -Djava.library.path=./DynamoDBLocal_lib -jar DynamoDBLocal.jar -sharedDb
   ```
**Note**  
DynamoDB processes incoming requests until you stop it\. To stop DynamoDB, type Ctrl\+C at the command prompt\.  
DynamoDB uses port 8000 by default\. If port 8000 is unavailable, this command throws an exception\. For a complete list of DynamoDB runtime options, including `-port` , type this command:  
`java -Djava.library.path=./DynamoDBLocal_lib -jar DynamoDBLocal.jar -help`

After completing these steps, you can start writing applications\.

### DynamoDB \(Downloadable Version\) and Apache Maven<a name="DynamoDBLocal.Maven"></a>

 To use DynamoDB in your application as a dependency:

1.  Download and install Apache Maven\. For more information, see [Downloading Apache Maven](https://maven.apache.org/download.cgi) and [Installing Apache Maven](https://maven.apache.org/install.html)\.

1.  Add the DynamoDB Maven repository to your application's Project Object Model \(POM\) file:

   ```
   <!--Dependency:-->
   <dependencies>
       <dependency>
          <groupId>com.amazonaws</groupId>
          <artifactId>DynamoDBLocal</artifactId>
          <version>[1.11,2.0)</version>
       </dependency>
   </dependencies>
   <!--Custom repository:-->
   <repositories>
       <repository>
          <id>dynamodb-local-oregon</id>
          <name>DynamoDB Local Release Repository</name>
          <url>https://s3-us-west-2.amazonaws.com/dynamodb-local/release</url>
       </repository>
   </repositories>
   ```
**Note**  
Alternatively, you can use one of the following repository URLs, depending on your region:    
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DynamoDBLocal.html)

 The aws\-dynamodb\-examples repository in GitHub contains examples for [ starting and stopping DynamoDB Local](https://github.com/awslabs/aws-dynamodb-examples/blob/master/src/test/java/com/amazonaws/services/dynamodbv2/DynamoDBLocalFixture.java) inside a Java program and [ using DynamoDB Local in JUnit tests](https://github.com/awslabs/aws-dynamodb-examples/blob/master/src/test/java/com/amazonaws/services/dynamodbv2/local/embedded/DynamoDBEmbeddedTest.java)\. 

### Command Line Options<a name="DynamoDBLocal.CommandLineOptions"></a>

You can use the following command line options with the downloadable version of DynamoDB:
+ `-cors` `value` — Enables support for cross\-origin resource sharing \(CORS\) for JavaScript\. You must provide a comma\-separated "allow" list of specific domains\. The default setting for `-cors` is an asterisk \(\*\), which allows public access\.
+ `-dbPath` `value` — The directory where DynamoDB writes its database file\. If you don't specify this option, the file is written to the current directory\. You can't specify both `-dbPath` and `-inMemory` at once\.
+ `-delayTransientStatuses` — Causes DynamoDB to introduce delays for certain operations\. DynamoDB \(Downloadable Version\) can perform some tasks almost instantaneously, such as create/update/delete operations on tables and indexes\. However, the DynamoDB service requires more time for these tasks\. Setting this parameter helps DynamoDB running on your computer simulate the behavior of the DynamoDB web service more closely\. \(Currently, this parameter introduces delays only for global secondary indexes that are in either *CREATING* or *DELETING* status\.\)
+ `-help` — Prints a usage summary and options\.
+ `-inMemory` — DynamoDB runs in memory instead of using a database file\. When you stop DynamoDB, none of the data is saved\. You can't specify both `-dbPath` and `-inMemory` at once\.
+ `-optimizeDbBeforeStartup` — Optimizes the underlying database tables before starting DynamoDB on your computer\. You also must specify `-dbPath` when you use this parameter\.
+ `-port` `value` — The port number that DynamoDB uses to communicate with your application\. If you don't specify this option, the default port is `8000`\.
**Note**  
DynamoDB uses port 8000 by default\. If port 8000 is unavailable, this command throws an exception\. You can use the `-port` option to specify a different port number\. For a complete list of DynamoDB runtime options, including `-port` , type this command:  
`java -Djava.library.path=./DynamoDBLocal_lib -jar DynamoDBLocal.jar -help`
+ `-sharedDb` — If you specify `-sharedDb`, DynamoDB uses a single database file instead of separate files for each credential and region\.