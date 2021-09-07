# Java and DAX<a name="DAX.client.run-application-java"></a>

DAX SDK for Java 2\.x is compatible with [AWS SDK for Java 2\.x](https://docs.aws.amazon.com/sdk-for-java/latest/developer-guide/)\. It's built on top of Java 8\+ and includes support for non\-blocking I/O\. For information about using DAX with AWS SDK for Java 1\.x, see [Using DAX with AWS SDK for Java 1\.x](DAX.client.java-sdk-v1.md)

## Using the client as a Maven dependency<a name="DAX.client.run-application-java.maven"></a>

Follow these steps to use the client for the DAX SDK for Java in your application as a dependency\.

1. Download and install Apache Maven\. For more information, see [Downloading Apache Maven](https://maven.apache.org/download.cgi) and [Installing Apache Maven](https://maven.apache.org/install.html)\.

1. Add the client Maven dependency to your application's Project Object Model \(POM\) file\. In this example, replace *x\.x\.x* with the actual version number of the client\.

   ```
   <!--Dependency:-->
   <dependencies>
       <dependency>
           <groupId>software.amazon.dax</groupId>
           <artifactId>amazon-dax-client</artifactId>
           <version>x.x.x</version>
       </dependency>
   </dependencies>
   ```

## TryDax sample code<a name="DAX.client.run-application-java.sample"></a>

After you've set up your workspace and added the DAX SDK as a dependency, copy [TryDax\.java](DAX.client.TryDax.java.md) into your project\.

Run the code using this command\.

```
java -cp classpath TryDax
```

You should see output similar to the following\.

```
Creating a DynamoDB client

Attempting to create table; please wait...
Successfully created table.  Table status: ACTIVE
Writing data to the table...
Writing 10 items for partition key: 1
Writing 10 items for partition key: 2
Writing 10 items for partition key: 3
...

Running GetItem and Query tests...
First iteration of each test will result in cache misses
Next iterations are cache hits

GetItem test - partition key 1-100 and sort keys 1-10
  Total time: 4390.240 ms - Avg time: 4.390 ms
  Total time: 3097.089 ms - Avg time: 3.097 ms
  Total time: 3273.463 ms - Avg time: 3.273 ms
  Total time: 3353.739 ms - Avg time: 3.354 ms
  Total time: 3533.314 ms - Avg time: 3.533 ms
Query test - partition key 1-100 and sort keys between 2 and 9
  Total time: 475.868 ms - Avg time: 4.759 ms
  Total time: 423.333 ms - Avg time: 4.233 ms
  Total time: 460.271 ms - Avg time: 4.603 ms
  Total time: 397.859 ms - Avg time: 3.979 ms
  Total time: 466.644 ms - Avg time: 4.666 ms

Attempting to delete table; please wait...
Successfully deleted table.
```

Take note of the timing information—the number of milliseconds required for the `GetItem` and `Query` tests\. In this case, you ran the program against the DynamoDB endpoint\. Now you'll run the program again, this time against your DAX cluster\.

To determine the endpoint of your DAX cluster, choose one of the following:
+ In the DynamoDB console, select your DAX cluster\. The cluster endpoint is shown in the console, as in the following example\.

  ```
  dax://my-cluster.l6fzcv.dax-clusters.us-east-1.amazonaws.com
  ```
+ Using the AWS CLI, enter the following command:

  ```
  aws dax describe-clusters --query "Clusters[*].ClusterDiscoveryEndpoint"
  ```

  The cluster endpoint address, port, and URL are shown in the output, as in the following example\.

  ```
  {
      "Address": "my-cluster.l6fzcv.dax-clusters.us-east-1.amazonaws.com",
      "Port": 8111,
      "URL": "dax://my-cluster.l6fzcv.dax-clusters.us-east-1.amazonaws.com"
  }
  ```

Now run the program again, but this time, specify the cluster endpoint URL as a command line parameter\.

```
java -cp classpath TryDax dax://my-cluster.l6fzcv.dax-clusters.us-east-1.amazonaws.com
```

Look at the output and take note of the timing information\. The elapsed times for `GetItem`and `Query` should be significantly lower with DAX than with DynamoDB\.

## SDK Metrics<a name="DAX.client.run-application-java.metrics"></a>

With DAX SDK for Java 2\.x, you can collect metrics about the service clients in your application and analyze the output in Amazon CloudWatch\. See [Enabling SDK metrics](https://docs.aws.amazon.com/sdk-for-java/latest/developer-guide/metrics.html) for more information\.

**Note**  
The DAX SDK for Java only collets `ApiCallSuccessful` and `ApiCallDuration` metrics\.