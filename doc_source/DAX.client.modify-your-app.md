# Modifying an existing application to use DAX<a name="DAX.client.modify-your-app"></a>

If you already have a Java application that uses Amazon DynamoDB, you can modify it so that it can access your DynamoDB Accelerator \(DAX\) cluster\. You don't have to rewrite the entire application because the DAX Java client is similar to the DynamoDB low\-level client included in the AWS SDK for Java 2\.x\. See [Working with items in DynamoDB](https://docs.aws.amazon.com/sdk-for-java/latest/developer-guide/examples-dynamodb-items.html) for details\.

**Note**  
This example uses AWS SDK for Java 2\.x\. For the legacy SDK for Java 1\.x version, see [Modifying an existing SDK for Java 1\.x application to use DAX](DAX.client.modify-your-app.java-sdk-v1.md)\.

To modify your program, replace the DynamoDB client with a DAX client\.

```
Region region = Region.US_EAST_1;

// Create an asynchronous DynamoDB client
DynamoDbAsyncClient client = DynamoDbAsyncClient.builder()
                .region(region)
                .build();

// Create an asynchronous DAX client
DynamoDbAsyncClient client = ClusterDaxAsyncClient.builder()
                .overrideConfiguration(Configuration.builder()
                    .url(<cluster url>) // for example, "dax://my-cluster.l6fzcv.dax-clusters.us-east-1.amazonaws.com"
                    .region(region)
                    .addMetricPublisher(cloudWatchMetricsPub) // optionally enable SDK metric collection
                    .build())
                .build();
```

You can also use the high\-level library that is part of the AWS SDK for Java 2\.x, replacing the DynamoDB client with a DAX client\.

```
Region region = Region.US_EAST_1;
DynamoDbAsyncClient dax = ClusterDaxAsyncClient.builder()
        .overrideConfiguration(Configuration.builder()
            .url(<cluster url>) // for example, "dax://my-cluster.l6fzcv.dax-clusters.us-east-1.amazonaws.com"
            .region(region)
            .build())
        .build();

DynamoDbEnhancedClient enhancedClient = DynamoDbEnhancedClient.builder()
        .dynamoDbClient(dax)
        .build();
```

For more information, see [Mapping items in DynamoDB tables](https://docs.aws.amazon.com/sdk-for-java/latest/developer-guide/examples-dynamodb-enhanced.html)\.