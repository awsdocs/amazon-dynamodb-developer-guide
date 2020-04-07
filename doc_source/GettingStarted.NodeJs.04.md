# Step 4: Query and Scan Data with AWS SDK for JavaScript in DynamoDB<a name="GettingStarted.NodeJs.04"></a>

You can use the `query` method to retrieve data from a table\. You must specify a partition key value; the sort key is optional\.

The primary key for the `Movies` table is composed of the following:
+ `year` – The partition key\. The attribute type is number\. 
+ `title` – The sort key\. The attribute type is string\.

To find all movies released during a year, you need to specify only the `year`\. You can also provide the `title` to retrieve a subset of movies based on some condition \(on the sort key\)\. For example, you can find movies released in 2014 that have a title starting with the letter "A"\.

In addition to the `query` method, you also can use the `scan` method, which can retrieve all the table data\.

To learn more about querying and scanning data, see [Working with Queries in DynamoDB](Query.md) and [Working with Scans in DynamoDB](Scan.md), respectively\.

**Topics**
+ [Step 4\.1: Query \- All Movies Released in a Year](#GettingStarted.NodeJs.04.Query.01)
+ [Step 4\.2: Query \- All Movies Released in a Year with Certain Titles](#GettingStarted.NodeJs.04.Query.02)
+ [Step 4\.3: Scan](#GettingStarted.NodeJs.04.Scan)

## Step 4\.1: Query \- All Movies Released in a Year<a name="GettingStarted.NodeJs.04.Query.01"></a>

The program included in this step retrieves all movies released in the `year` 1985\.

1. Copy the following program and paste it into a file named `MoviesQuery01.js`\.

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
   var AWS = require("aws-sdk");
   
   AWS.config.update({
     region: "us-west-2",
     endpoint: "http://localhost:8000"
   });
   
   var docClient = new AWS.DynamoDB.DocumentClient();
   
   console.log("Querying for movies from 1985.");
   
   var params = {
       TableName : "Movies",
       KeyConditionExpression: "#yr = :yyyy",
       ExpressionAttributeNames:{
           "#yr": "year"
       },
       ExpressionAttributeValues: {
           ":yyyy": 1985
       }
   };
   
   docClient.query(params, function(err, data) {
       if (err) {
           console.error("Unable to query. Error:", JSON.stringify(err, null, 2));
       } else {
           console.log("Query succeeded.");
           data.Items.forEach(function(item) {
               console.log(" -", item.year + ": " + item.title);
           });
       }
   });
   ```
**Note**  
`ExpressionAttributeNames` provides name substitution\. You use this because `year` is a reserved word in Amazon DynamoDB\. You can't use it directly in any expression, including `KeyConditionExpression`\. You use the expression attribute name `#yr` to address this\.  
`ExpressionAttributeValues` provides value substitution\. You use this because you cannot use literals in any expression, including `KeyConditionExpression`\. You use the expression attribute value `:yyyy` to address this\.

1. To run the program, enter the following command\.

   `node MoviesQuery01.js`

**Note**  
The preceding program shows how to query a table by its primary key attributes\. In DynamoDB, you can optionally create one or more secondary indexes on a table, and query those indexes in the same way that you query a table\. Secondary indexes give your applications additional flexibility by allowing queries on non\-key attributes\. For more information, see [Improving Data Access with Secondary Indexes](SecondaryIndexes.md)\. 

## Step 4\.2: Query \- All Movies Released in a Year with Certain Titles<a name="GettingStarted.NodeJs.04.Query.02"></a>

The program included in this step retrieves all movies released in `year` 1992, with `title` beginning with the letter "A" through the letter "L"\.

1. Copy the following program and paste it into a file named `MoviesQuery02.js`\.

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
   var AWS = require("aws-sdk");
   
   AWS.config.update({
     region: "us-west-2",
     endpoint: "http://localhost:8000"
   });
   
   var docClient = new AWS.DynamoDB.DocumentClient();
   
   console.log("Querying for movies from 1992 - titles A-L, with genres and lead actor");
   
   var params = {
       TableName : "Movies",
       ProjectionExpression:"#yr, title, info.genres, info.actors[0]",
       KeyConditionExpression: "#yr = :yyyy and title between :letter1 and :letter2",
       ExpressionAttributeNames:{
           "#yr": "year"
       },
       ExpressionAttributeValues: {
           ":yyyy": 1992,
           ":letter1": "A",
           ":letter2": "L"
       }
   };
   
   docClient.query(params, function(err, data) {
       if (err) {
           console.log("Unable to query. Error:", JSON.stringify(err, null, 2));
       } else {
           console.log("Query succeeded.");
           data.Items.forEach(function(item) {
               console.log(" -", item.year + ": " + item.title
               + " ... " + item.info.genres
               + " ... " + item.info.actors[0]);
           });
       }
   });
   ```

1. To run the program, enter the following command\.

   `node MoviesQuery02.js`

## Step 4\.3: Scan<a name="GettingStarted.NodeJs.04.Scan"></a>

The `scan` method reads every item in the table and returns all the data in the table\. You can provide an optional `filter_expression`, so that only the items matching your criteria are returned\. However, the filter is applied only after the entire table has been scanned\.

The following program scans the entire `Movies` table, which contains approximately 5,000 items\. The scan specifies the optional filter to retrieve only the movies from the 1950s \(approximately 100 items\), and discard all of the others\.

1. Copy the following program and paste it into a file named `MoviesScan.js`\.

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
   var AWS = require("aws-sdk");
   
   AWS.config.update({
       region: "us-west-2",
       endpoint: "http://localhost:8000"
   });
   
   var docClient = new AWS.DynamoDB.DocumentClient();
   
   var params = {
       TableName: "Movies",
       ProjectionExpression: "#yr, title, info.rating",
       FilterExpression: "#yr between :start_yr and :end_yr",
       ExpressionAttributeNames: {
           "#yr": "year",
       },
       ExpressionAttributeValues: {
            ":start_yr": 1950,
            ":end_yr": 1959 
       }
   };
   
   console.log("Scanning Movies table.");
   docClient.scan(params, onScan);
   
   function onScan(err, data) {
       if (err) {
           console.error("Unable to scan the table. Error JSON:", JSON.stringify(err, null, 2));
       } else {
           // print all the movies
           console.log("Scan succeeded.");
           data.Items.forEach(function(movie) {
              console.log(
                   movie.year + ": ",
                   movie.title, "- rating:", movie.info.rating);
           });
   
           // continue scanning if we have more movies, because
           // scan can retrieve a maximum of 1MB of data
           if (typeof data.LastEvaluatedKey != "undefined") {
               console.log("Scanning for more...");
               params.ExclusiveStartKey = data.LastEvaluatedKey;
               docClient.scan(params, onScan);
           }
       }
   }
   ```

   In the code, note the following:
   + `ProjectionExpression` specifies the attributes you want in the scan result\.
   + `FilterExpression` specifies a condition that returns only items that satisfy the condition\. All other items are discarded\.

1. To run the program, enter the following command\.

   `node MoviesScan.js`

**Note**  
You can also use the `Scan` operation with any secondary indexes that you have created on the table\. For more information, see [Improving Data Access with Secondary Indexes](SecondaryIndexes.md)\. 