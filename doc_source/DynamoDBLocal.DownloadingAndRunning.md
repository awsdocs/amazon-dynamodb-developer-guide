# Deploying DynamoDB locally on your computer<a name="DynamoDBLocal.DownloadingAndRunning"></a>

------
#### [ Download locally ]

DynamoDB local is provided as an executable `.jar` file\. The application runs on Windows, Linux, macOS, and other platforms that support Java\.

Follow these steps to set up and run DynamoDB on your computer\.

**Note**  
 DynamoDB local v1\.20 and greater is required for use on M1 and newer Apple processors\. 

**To set up DynamoDB on your computer**

1. Download DynamoDB for free from one of the following locations\.  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DynamoDBLocal.DownloadingAndRunning.html)

   DynamoDB is also available as part of the AWS Toolkit for Eclipse\. For more information, see [AWS Toolkit for Eclipse](https://aws.amazon.com/eclipse/)\.
**Important**  
To run DynamoDB on your computer, you must have the Java Runtime Environment \(JRE\) version 8\.x or newer\. The application doesn't run on earlier JRE versions\.

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

------
#### [ Docker ]

The downloadable version of Amazon DynamoDB is available as a Docker image\. For more information, see [dynamodb\-local](https://hub.docker.com/r/amazon/dynamodb-local)\.

 For an example of using DynamoDB local as part of a REST application built on the AWS Serverless Application Model \(AWS SAM\), see [SAM DynamoDB application for managing orders](https://github.com/aws-samples/aws-sam-java-rest)\. This sample application demonstrates how to use DynamoDB local for testing\. 

If you want to run a multi\-container application that also uses the DynamoDB local container, use Docker Compose to define and run all the services in your application, including DynamoDB local\.

**To install and run DynamoDB local with Docker compose:**

1. Download and install [Docker desktop](https://www.docker.com/products/docker-desktop)\.

1. Copy the following code to a file and save it as `docker-compose.yml`\.

   ```
   version: '3.8'
   services:
     dynamodb-local:
       command: "-jar DynamoDBLocal.jar -sharedDb -dbPath ./data"
       image: "amazon/dynamodb-local:latest"
       container_name: dynamodb-local
       ports:
         - "8000:8000"
       volumes:
         - "./docker/dynamodb:/home/dynamodblocal/data"
       working_dir: /home/dynamodblocal
   ```

   If you want your application and DynamoDB local to be in separate containers, use the following yaml file\.

   ```
   version: '3.8'
   services:
     dynamodb-local:
       command: "-jar DynamoDBLocal.jar -sharedDb -dbPath ./data"
       image: "amazon/dynamodb-local:latest"
       container_name: dynamodb-local
       ports:
         - "8000:8000"
       volumes:
         - "./docker/dynamodb:/home/dynamodblocal/data"
       working_dir: /home/dynamodblocal
     app-node:
       depends_on:
         - dynamodb-local
       image: amazon/aws-cli
       container_name: app-node
       ports:
        - "8080:8080"
       environment:
         AWS_ACCESS_KEY_ID: 'DUMMYIDEXAMPLE'
         AWS_SECRET_ACCESS_KEY: 'DUMMYEXAMPLEKEY'
       command:
         dynamodb describe-limits --endpoint-url http://dynamodb-local:8000 --region us-west-2
   ```

   This docker\-compose\.yml script creates an `app-node` container and a `dynamodb-local` container\. The script runs a command in the `app-node` container that uses the AWS CLI to connect to the `dynamodb-local` container and describes the account and table limits\.

   To use with your own application image, replace the `image` value in the example below with that of your application\.

   ```
   version: '3.8'
   services:
     dynamodb-local:
       command: "-jar DynamoDBLocal.jar -sharedDb -dbPath ./data"
       image: "amazon/dynamodb-local:latest"
       container_name: dynamodb-local
       ports:
         - "8000:8000"
       volumes:
         - "./docker/dynamodb:/home/dynamodblocal/data"
       working_dir: /home/dynamodblocal
     app-node:
       image: location-of-your-dynamodb-demo-app:latest
       container_name: app-node
       ports:
         - "8080:8080"
       depends_on:
         - "dynamodb-local"
       links:
         - "dynamodb-local"
       environment:
         AWS_ACCESS_KEY_ID: 'DUMMYIDEXAMPLE'
         AWS_SECRET_ACCESS_KEY: 'DUMMYEXAMPLEKEY'
         REGION: 'eu-west-1'
   ```
**Note**  
The YAML scripts require that you specify an AWS access key and an AWS secret key, but they are not required to be valid AWS keys for you to access DynamoDB local\.

1. Run the following command\-line command:

   ```
   docker-compose up
   ```

------
#### [ Apache Maven ]

Follow these steps to use Amazon DynamoDB in your application as a dependency\.

**To deploy DynamoDB as an Apache Maven repository**

1.  Download and install Apache Maven\. For more information, see [Downloading Apache Maven](https://maven.apache.org/download.cgi) and [Installing Apache Maven](https://maven.apache.org/install.html)\.

1.  Add the DynamoDB Maven repository to your application's Project Object Model \(POM\) file\.

   ```
   <!--Dependency:-->
   <dependencies>
       <dependency>
          <groupId>com.amazonaws</groupId>
          <artifactId>DynamoDBLocal</artifactId>
          <version>[1.12,2.0)</version>
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
You can also use one of the following repository URLs, depending on your AWS Region\.    
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DynamoDBLocal.DownloadingAndRunning.html)

------