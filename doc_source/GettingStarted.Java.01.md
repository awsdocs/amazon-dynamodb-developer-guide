# Step 1: Create a Table<a name="GettingStarted.Java.01"></a>

In this step, you create a table named `Movies`\. The primary key for the table is composed of the following attributes:
+ `year` – The partition key\. The `ScalarAttributeType` is `N` for number\.
+ `title` – The sort key\. The `ScalarAttributeType` is `S` for string\.

1. Copy and paste the following program into your Java development environment.

   ```
   // Copyright 2012-2015 Amazon.com, Inc. or its affiliates. All Rights Reserved.
   // Licensed under the Apache License, Version 2.0.
   
   package com.amazonaws.codesamples.gsg;
   
   import java.util.Arrays;
   
   import com.amazonaws.client.builder.AwsClientBuilder;
   import com.amazonaws.services.dynamodbv2.AmazonDynamoDB;
   import com.amazonaws.services.dynamodbv2.AmazonDynamoDBClientBuilder;
   import com.amazonaws.services.dynamodbv2.document.DynamoDB;
   import com.amazonaws.services.dynamodbv2.document.Table;
   import com.amazonaws.services.dynamodbv2.model.AttributeDefinition;
   import com.amazonaws.services.dynamodbv2.model.KeySchemaElement;
   import com.amazonaws.services.dynamodbv2.model.KeyType;
   import com.amazonaws.services.dynamodbv2.model.ProvisionedThroughput;
   import com.amazonaws.services.dynamodbv2.model.ScalarAttributeType;
   
   public class MoviesCreateTable {
   
       public static void main(String[] args) throws Exception {
   
           AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard()
               .withEndpointConfiguration(new AwsClientBuilder.EndpointConfiguration("http://localhost:8000", "us-west-2"))
               .build();
   
           DynamoDB dynamoDB = new DynamoDB(client);
   
           String tableName = "Movies";
   
           try {
               System.out.println("Attempting to create table; please wait...");
               Table table = dynamoDB.createTable(tableName,
                   Arrays.asList(new KeySchemaElement("year", KeyType.HASH), // Partition
                                                                             // key
                       new KeySchemaElement("title", KeyType.RANGE)), // Sort key
                   Arrays.asList(new AttributeDefinition("year", ScalarAttributeType.N),
                       new AttributeDefinition("title", ScalarAttributeType.S)),
                   new ProvisionedThroughput(10L, 10L));
               table.waitForActive();
               System.out.println("Success.  Table status: " + table.getDescription().getTableStatus());
   
           }
           catch (Exception e) {
               System.err.println("Unable to create table: ");
               System.err.println(e.getMessage());
           }
   
       }
   }
   ```
**Note**  
You set the endpoint to indicate that you are creating the table in DynamoDB on your computer\.
In the `createTable` call, you specify table name, primary key attributes, and its data types\.
The `ProvisionedThroughput` parameter is required, but the downloadable version of DynamoDB ignores it\. \(Provisioned throughput is beyond the scope of this exercise\.\)

1. Compile and run the program\.

To learn more about managing tables, see [Working with Tables in DynamoDB](WorkingWithTables.md)\.
