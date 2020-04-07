# Step 4: Query and Scan the Data<a name="GettingStarted.Java.04"></a>

You can use the `query` method to retrieve data from a table\. You must specify a partition key value\. The sort key is optional\.

The primary key for the `Movies` table is composed of the following:
+ `year` – The partition key\. The attribute type is number\. 
+ `title` – The sort key\. The attribute type is string\.

To find all movies released during a year, you need to specify only the `year`\. You can also provide the `title` to retrieve a subset of movies based on some condition \(on the sort key\)\. For example, you can find movies released in 2014 that have a title starting with the letter "A"\.

In addition to the `query` method, there is also a `scan` method that can retrieve all of the table data\.

To learn more about querying and scanning data, see [Working with Queries in DynamoDB](Query.md) and [Working with Scans in DynamoDB](Scan.md), respectively\. 

**Topics**
+ [Step 4\.1: Query](#GettingStarted.Java.04.Query)
+ [Step 4\.2: Scan](#GettingStarted.Java.04.Scan)

## Step 4\.1: Query<a name="GettingStarted.Java.04.Query"></a>

The code included in this step performs the following queries:
+ Retrieve all movies released in the `year` 1985\.
+ Retrieve all movies released in the `year` 1992, with a `title` beginning with the letter "A" through the letter "L"\.

1. Copy the following program and paste it into your Java development environment\.

   ```
   /**
    * Copyright 2010-2019 Amazon.com, Inc. or its affiliates. All Rights Reserved.
    *
    * This file is licensed under the Apache License, Version 2.0 (the "License").
    * You may not use this file except in compliance with the License. A copy of
    * the License is located at
    *
    * http://aws.amazon.com/apache2.0/
    *
    * This file is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR
    * CONDITIONS OF ANY KIND, either express or implied. See the License for the
    * specific language governing permissions and limitations under the License.
   */
   
   
   
   package com.amazonaws.codesamples.gsg;
   
   import java.util.HashMap;
   import java.util.Iterator;
   
   import com.amazonaws.client.builder.AwsClientBuilder;
   import com.amazonaws.services.dynamodbv2.AmazonDynamoDB;
   import com.amazonaws.services.dynamodbv2.AmazonDynamoDBClientBuilder;
   import com.amazonaws.services.dynamodbv2.document.DynamoDB;
   import com.amazonaws.services.dynamodbv2.document.Item;
   import com.amazonaws.services.dynamodbv2.document.ItemCollection;
   import com.amazonaws.services.dynamodbv2.document.QueryOutcome;
   import com.amazonaws.services.dynamodbv2.document.Table;
   import com.amazonaws.services.dynamodbv2.document.spec.QuerySpec;
   
   public class MoviesQuery {
   
       public static void main(String[] args) throws Exception {
   
           AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard()
               .withEndpointConfiguration(new AwsClientBuilder.EndpointConfiguration("http://localhost:8000", "us-west-2"))
               .build();
   
           DynamoDB dynamoDB = new DynamoDB(client);
   
           Table table = dynamoDB.getTable("Movies");
   
           HashMap<String, String> nameMap = new HashMap<String, String>();
           nameMap.put("#yr", "year");
   
           HashMap<String, Object> valueMap = new HashMap<String, Object>();
           valueMap.put(":yyyy", 1985);
   
           QuerySpec querySpec = new QuerySpec().withKeyConditionExpression("#yr = :yyyy").withNameMap(nameMap)
               .withValueMap(valueMap);
   
           ItemCollection<QueryOutcome> items = null;
           Iterator<Item> iterator = null;
           Item item = null;
   
           try {
               System.out.println("Movies from 1985");
               items = table.query(querySpec);
   
               iterator = items.iterator();
               while (iterator.hasNext()) {
                   item = iterator.next();
                   System.out.println(item.getNumber("year") + ": " + item.getString("title"));
               }
   
           }
           catch (Exception e) {
               System.err.println("Unable to query movies from 1985");
               System.err.println(e.getMessage());
           }
   
           valueMap.put(":yyyy", 1992);
           valueMap.put(":letter1", "A");
           valueMap.put(":letter2", "L");
   
           querySpec.withProjectionExpression("#yr, title, info.genres, info.actors[0]")
               .withKeyConditionExpression("#yr = :yyyy and title between :letter1 and :letter2").withNameMap(nameMap)
               .withValueMap(valueMap);
   
           try {
               System.out.println("Movies from 1992 - titles A-L, with genres and lead actor");
               items = table.query(querySpec);
   
               iterator = items.iterator();
               while (iterator.hasNext()) {
                   item = iterator.next();
                   System.out.println(item.getNumber("year") + ": " + item.getString("title") + " " + item.getMap("info"));
               }
   
           }
           catch (Exception e) {
               System.err.println("Unable to query movies from 1992:");
               System.err.println(e.getMessage());
           }
       }
   }
   ```
**Note**  
`nameMap` provides name substitution\. This is used because `year` is a reserved word in Amazon DynamoDB\. You can't use it directly in any expression, including `KeyConditionExpression`\. You use the expression attribute name `#yr` to address this\.
`valueMap` provides value substitution\. This is used because you can't use literals in any expression, including `KeyConditionExpression`\. You use the expression attribute value `:yyyy` to address this\.

   First, you create the `querySpec` object, which describes the query parameters, and then you pass the object to the `query` method\.

1. Compile and run the program\.

**Note**  
The preceding program shows how to query a table by its primary key attributes\. In DynamoDB, you can optionally create one or more secondary indexes on a table, and query those indexes in the same way that you query a table\. Secondary indexes give your applications additional flexibility by allowing queries on non\-key attributes\. For more information, see [Improving Data Access with Secondary Indexes](SecondaryIndexes.md)\. 

## Step 4\.2: Scan<a name="GettingStarted.Java.04.Scan"></a>

The `scan` method reads every item in the entire table and returns all the data in the table\. You can provide an optional `filter_expression` so that only the items matching your criteria are returned\. However, the filter is applied only after the entire table has been scanned\.

The following program scans the entire `Movies` table, which contains approximately 5,000 items\. The scan specifies the optional filter to retrieve only the movies from the 1950s \(approximately 100 items\) and discard all the others\.

1. Copy the following program and paste it into your Java development environment\.

   ```
   /**
    * Copyright 2010-2019 Amazon.com, Inc. or its affiliates. All Rights Reserved.
    *
    * This file is licensed under the Apache License, Version 2.0 (the "License").
    * You may not use this file except in compliance with the License. A copy of
    * the License is located at
    *
    * http://aws.amazon.com/apache2.0/
    *
    * This file is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR
    * CONDITIONS OF ANY KIND, either express or implied. See the License for the
    * specific language governing permissions and limitations under the License.
   */
   
   
   
   package com.amazonaws.codesamples.gsg;
   
   import java.util.Iterator;
   
   import com.amazonaws.client.builder.AwsClientBuilder;
   import com.amazonaws.services.dynamodbv2.AmazonDynamoDB;
   import com.amazonaws.services.dynamodbv2.AmazonDynamoDBClientBuilder;
   import com.amazonaws.services.dynamodbv2.document.DynamoDB;
   import com.amazonaws.services.dynamodbv2.document.Item;
   import com.amazonaws.services.dynamodbv2.document.ItemCollection;
   import com.amazonaws.services.dynamodbv2.document.ScanOutcome;
   import com.amazonaws.services.dynamodbv2.document.Table;
   import com.amazonaws.services.dynamodbv2.document.spec.ScanSpec;
   import com.amazonaws.services.dynamodbv2.document.utils.NameMap;
   import com.amazonaws.services.dynamodbv2.document.utils.ValueMap;
   
   public class MoviesScan {
   
       public static void main(String[] args) throws Exception {
   
           AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard()
               .withEndpointConfiguration(new AwsClientBuilder.EndpointConfiguration("http://localhost:8000", "us-west-2"))
               .build();
   
           DynamoDB dynamoDB = new DynamoDB(client);
   
           Table table = dynamoDB.getTable("Movies");
   
           ScanSpec scanSpec = new ScanSpec().withProjectionExpression("#yr, title, info.rating")
               .withFilterExpression("#yr between :start_yr and :end_yr").withNameMap(new NameMap().with("#yr", "year"))
               .withValueMap(new ValueMap().withNumber(":start_yr", 1950).withNumber(":end_yr", 1959));
   
           try {
               ItemCollection<ScanOutcome> items = table.scan(scanSpec);
   
               Iterator<Item> iter = items.iterator();
               while (iter.hasNext()) {
                   Item item = iter.next();
                   System.out.println(item.toString());
               }
   
           }
           catch (Exception e) {
               System.err.println("Unable to scan the table:");
               System.err.println(e.getMessage());
           }
       }
   }
   ```

   In the code, note the following:
   + `ProjectionExpression` specifies the attributes you want in the scan result\.
   + `FilterExpression` specifies a condition that returns only items that satisfy the condition\. All other items are discarded\.

1. Compile and run the program\.

**Note**  
You can also use the `Scan` operation with any secondary indexes that you created on the table\. For more information, see [Improving Data Access with Secondary Indexes](SecondaryIndexes.md)\. 