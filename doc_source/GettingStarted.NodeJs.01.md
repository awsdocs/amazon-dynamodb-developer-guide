# Step 1: Create a Table with in DynamoDB with AWS SDK for JavaScript<a name="GettingStarted.NodeJs.01"></a>

In this step, you create a table named `Movies`\. The primary key for the table is composed of the following attributes:
+ `year` – The partition key\. The `AttributeType` is `N` for number\.
+ `title` – The sort key\. The `AttributeType` is `S` for string\.

1. Copy the following program and paste it into a file named `MoviesCreateTable.js`\.

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
   
   var dynamodb = new AWS.DynamoDB();
   
   var params = {
       TableName : "Movies",
       KeySchema: [       
           { AttributeName: "year", KeyType: "HASH"},  //Partition key
           { AttributeName: "title", KeyType: "RANGE" }  //Sort key
       ],
       AttributeDefinitions: [       
           { AttributeName: "year", AttributeType: "N" },
           { AttributeName: "title", AttributeType: "S" }
       ],
       ProvisionedThroughput: {       
           ReadCapacityUnits: 10, 
           WriteCapacityUnits: 10
       }
   };
   
   dynamodb.createTable(params, function(err, data) {
       if (err) {
           console.error("Unable to create table. Error JSON:", JSON.stringify(err, null, 2));
       } else {
           console.log("Created table. Table description JSON:", JSON.stringify(data, null, 2));
       }
   });
   ```
**Note**  
You set the endpoint to indicate that you are creating the table in Amazon DynamoDB on your computer\.
In the `createTable` call, you specify table name, primary key attributes, and its data types\.
The `ProvisionedThroughput` parameter is required, but the downloadable version of DynamoDB ignores it\. \(Provisioned throughput is beyond the scope of this tutorial\.\)

1. To run the program, enter the following command\.

   `node MoviesCreateTable.js`

To learn more about managing tables, see [Working with Tables and Data in DynamoDB](WorkingWithTables.md)\.