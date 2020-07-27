# Deploying DynamoDB Local by Adding an Apache Maven Repository<a name="DynamoDBLocal.Maven"></a>

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
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DynamoDBLocal.Maven.html)

 The `aws-dynamodb-examples` repository in GitHub contains examples for [starting and stopping DynamoDB local](https://github.com/awslabs/aws-dynamodb-examples/blob/master/src/test/java/com/amazonaws/services/dynamodbv2/DynamoDBLocalFixture.java) inside a Java program and [using DynamoDB local in JUnit tests](https://github.com/awslabs/aws-dynamodb-examples/blob/master/src/test/java/com/amazonaws/services/dynamodbv2/local/embedded/DynamoDBEmbeddedTest.java)\. 