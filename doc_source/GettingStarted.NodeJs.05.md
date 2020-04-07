# Step 5: \(Optional\): Delete the Table using AWS SDK for JavaScript<a name="GettingStarted.NodeJs.05"></a>

 To delete the `Movies` table: 

1. Copy the following program and paste it into a file named `MoviesDeleteTable.js`\.

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
       TableName : "Movies"
   };
   
   dynamodb.deleteTable(params, function(err, data) {
       if (err) {
           console.error("Unable to delete table. Error JSON:", JSON.stringify(err, null, 2));
       } else {
           console.log("Deleted table. Table description JSON:", JSON.stringify(data, null, 2));
       }
   });
   ```

1. To run the program, enter the following command\.

   `node MoviesDeleteTable.js`