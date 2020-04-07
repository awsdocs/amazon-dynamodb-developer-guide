# Step 2: Load Sample Data with Java and DynamoDB<a name="GettingStarted.Java.02"></a>

In this step, you populate the `Movies` table with sample data\.

**Topics**
+ [Step 2\.1: Download the Sample Data File](#GettingStarted.Java.02.01)
+ [Step 2\.2: Load the Sample Data into the Movies Table](#GettingStarted.Java.02.02)

This scenario uses a sample data file that contains information about a few thousand movies from the Internet Movie Database \(IMDb\)\. The movie data is in JSON format, as shown in the following example\. For each movie, there is a `year`, a `title`, and a JSON map named `info`\. 

```
[
   {
      "year" : ... ,
      "title" : ... ,
      "info" : { ... }
   },
   {
      "year" : ...,
      "title" : ...,
      "info" : { ... }
   },

    ...

]
```

In the JSON data, note the following:
+ You use the `year` and `title` as the primary key attribute values for the `Movies` table\.
+ You store the rest of the `info` values in a single attribute called `info`\. This program illustrates how you can store JSON in an Amazon DynamoDB attribute\. 

 The following is an example of movie data\.

```
{
    "year" : 2013,
    "title" : "Turn It Down, Or Else!",
    "info" : {
        "directors" : [
            "Alice Smith",
            "Bob Jones"
        ],
        "release_date" : "2013-01-18T00:00:00Z",
        "rating" : 6.2,
        "genres" : [
            "Comedy",
            "Drama"
        ],
        "image_url" : "http://ia.media-imdb.com/images/N/O9ERWAU7FS797AJ7LU8HN09AMUP908RLlo5JF90EWR7LJKQ7@@._V1_SX400_.jpg",
        "plot" : "A rock band plays their music at high volumes, annoying the neighbors.",
        "rank" : 11,
        "running_time_secs" : 5215,
        "actors" : [
            "David Matthewman",
            "Ann Thomas",
            "Jonathan G. Neff"
       ]
    }
}
```

## Step 2\.1: Download the Sample Data File<a name="GettingStarted.Java.02.01"></a>

1. Download the sample data archive: [moviedata\.zip](samples/moviedata.zip)

1. Extract the data file \(`moviedata.json`\) from the archive\.

1. Copy and paste the `moviedata.json` file into your current directory\.

## Step 2\.2: Load the Sample Data into the Movies Table<a name="GettingStarted.Java.02.02"></a>

After you download the sample data, you can run the following program to populate the `Movies` table\.

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
   
   import java.io.File;
   import java.util.Iterator;
   
   import com.amazonaws.client.builder.AwsClientBuilder;
   import com.amazonaws.services.dynamodbv2.AmazonDynamoDB;
   import com.amazonaws.services.dynamodbv2.AmazonDynamoDBClientBuilder;
   import com.amazonaws.services.dynamodbv2.document.DynamoDB;
   import com.amazonaws.services.dynamodbv2.document.Item;
   import com.amazonaws.services.dynamodbv2.document.Table;
   import com.fasterxml.jackson.core.JsonFactory;
   import com.fasterxml.jackson.core.JsonParser;
   import com.fasterxml.jackson.databind.JsonNode;
   import com.fasterxml.jackson.databind.ObjectMapper;
   import com.fasterxml.jackson.databind.node.ObjectNode;
   
   public class MoviesLoadData {
   
       public static void main(String[] args) throws Exception {
   
           AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard()
               .withEndpointConfiguration(new AwsClientBuilder.EndpointConfiguration("http://localhost:8000", "us-west-2"))
               .build();
   
           DynamoDB dynamoDB = new DynamoDB(client);
   
           Table table = dynamoDB.getTable("Movies");
   
           JsonParser parser = new JsonFactory().createParser(new File("moviedata.json"));
   
           JsonNode rootNode = new ObjectMapper().readTree(parser);
           Iterator<JsonNode> iter = rootNode.iterator();
   
           ObjectNode currentNode;
   
           while (iter.hasNext()) {
               currentNode = (ObjectNode) iter.next();
   
               int year = currentNode.path("year").asInt();
               String title = currentNode.path("title").asText();
   
               try {
                   table.putItem(new Item().withPrimaryKey("year", year, "title", title).withJSON("info",
                       currentNode.path("info").toString()));
                   System.out.println("PutItem succeeded: " + year + " " + title);
   
               }
               catch (Exception e) {
                   System.err.println("Unable to add movie: " + year + " " + title);
                   System.err.println(e.getMessage());
                   break;
               }
           }
           parser.close();
       }
   }
   ```

   This program uses the open source Jackson library to process JSON\. Jackson is included in the AWS SDK for Java\. You don't have to install it separately\.

1. Compile and run the program\.