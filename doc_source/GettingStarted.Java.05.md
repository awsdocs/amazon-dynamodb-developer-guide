# Step 5: \(Optional\) Delete the Table<a name="GettingStarted.Java.05"></a>

 To delete the `Movies` table: 

1. Copy and paste the following program into your Java development environment.

   ```
   // Copyright 2012-2015 Amazon.com, Inc. or its affiliates. All Rights Reserved.
   // Licensed under the Apache License, Version 2.0.
   
   package com.amazonaws.codesamples.gsg;
   
   import com.amazonaws.client.builder.AwsClientBuilder;
   import com.amazonaws.services.dynamodbv2.AmazonDynamoDB;
   import com.amazonaws.services.dynamodbv2.AmazonDynamoDBClientBuilder;
   import com.amazonaws.services.dynamodbv2.document.DynamoDB;
   import com.amazonaws.services.dynamodbv2.document.Table;
   
   public class MoviesDeleteTable {
   
       public static void main(String[] args) throws Exception {
   
           AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard()
               .withEndpointConfiguration(new AwsClientBuilder.EndpointConfiguration("http://localhost:8000", "us-west-2"))
               .build();
   
           DynamoDB dynamoDB = new DynamoDB(client);
   
           Table table = dynamoDB.getTable("Movies");
   
           try {
               System.out.println("Attempting to delete table; please wait...");
               table.delete();
               table.waitForDelete();
               System.out.print("Success.");
   
           }
           catch (Exception e) {
               System.err.println("Unable to delete table: ");
               System.err.println(e.getMessage());
           }
       }
   }
   ```

1. Compile and run the program\.
