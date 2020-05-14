# Step 3: Create, Read, Update, and Delete an Item<a name="GettingStarted.NodeJs.03"></a>

In this step, you perform read and write operations on an item in the `Movies` table\.

To learn more about reading and writing data, see [Working with Items and Attributes](WorkingWithItems.md)\.

**Topics**
+ [Step 3\.1: Create a New Item](#GettingStarted.NodeJs.03.01)
+ [Step 3\.2: Read an Item](#GettingStarted.NodeJs.03.02)
+ [Step 3\.3: Update an Item](#GettingStarted.NodeJs.03.03)
+ [Step 3\.4: Increment an Atomic Counter](#GettingStarted.NodeJs.03.04)
+ [Step 3\.5: Update an Item \(Conditionally\)](#GettingStarted.NodeJs.03.05)
+ [Step 3\.6: Delete an Item](#GettingStarted.NodeJs.03.06)

## Step 3\.1: Create a New Item<a name="GettingStarted.NodeJs.03.01"></a>

In this step, you add a new item to the `Movies` table\.

1. Copy the following program and paste it into a file named `MoviesItemOps01.js`\.

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
   
   var table = "Movies";
   
   var year = 2015;
   var title = "The Big New Movie";
   
   var params = {
       TableName:table,
       Item:{
           "year": year,
           "title": title,
           "info":{
               "plot": "Nothing happens at all.",
               "rating": 0
           }
       }
   };
   
   console.log("Adding a new item...");
   docClient.put(params, function(err, data) {
       if (err) {
           console.error("Unable to add item. Error JSON:", JSON.stringify(err, null, 2));
       } else {
           console.log("Added item:", JSON.stringify(data, null, 2));
       }
   });
   ```
**Note**  
The primary key is required\. This code adds an item that has a primary key \(`year`, `title`\) and `info` attributes\. The `info` attribute stores sample JSON that provides more information about the movie\.

1. To run the program, enter the following command\.

   `node MoviesItemOps01.js`

## Step 3\.2: Read an Item<a name="GettingStarted.NodeJs.03.02"></a>

In the previous program, you added the following item to the table\.

```
{
   year: 2015,
   title: "The Big New Movie",
   info: {
        plot: "Nothing happens at all.",
        rating: 0
   }
}
```

You can use the `get` method to read the item from the `Movies` table\. You must specify the primary key values so that you can read any item from `Movies` if you know its `year` and `title`\. 

1. Copy the following program and paste it into a file named `MoviesItemOps02.js`\.

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
   
   var table = "Movies";
   
   var year = 2015;
   var title = "The Big New Movie";
   
   var params = {
       TableName: table,
       Key:{
           "year": year,
           "title": title
       }
   };
   
   docClient.get(params, function(err, data) {
       if (err) {
           console.error("Unable to read item. Error JSON:", JSON.stringify(err, null, 2));
       } else {
           console.log("GetItem succeeded:", JSON.stringify(data, null, 2));
       }
   });
   ```

1. To run the program, enter the following command\.

   `node MoviesItemOps02.js`

## Step 3\.3: Update an Item<a name="GettingStarted.NodeJs.03.03"></a>

You can use the `update` method to modify an existing item\. You can update values of existing attributes, add new attributes, or remove attributes\.

In this example, you perform the following updates:
+ Change the value of the existing attributes \(`rating`, `plot`\)\. 
+ Add a new list attribute \(`actors`\) to the existing `info` map\.

Previously, you added the following item to the table\.

```
{
   year: 2015,
   title: "The Big New Movie",
   info: {
        plot: "Nothing happens at all.",
        rating: 0
   }
}
```

The item is updated as follows\.

```
{
   year: 2015,
   title: "The Big New Movie",
   info: {
           plot: "Everything happens all at once.",
           rating: 5.5,
           actors: ["Larry", "Moe", "Curly"]
   }
}
```

1. Copy the following program and paste it into a file named `MoviesItemOps03.js`\.

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
   
   var docClient = new AWS.DynamoDB.DocumentClient()
   
   var table = "Movies";
   
   var year = 2015;
   var title = "The Big New Movie";
   
   // Update the item, unconditionally,
   
   var params = {
       TableName:table,
       Key:{
           "year": year,
           "title": title
       },
       UpdateExpression: "set info.rating = :r, info.plot=:p, info.actors=:a",
       ExpressionAttributeValues:{
           ":r":5.5,
           ":p":"Everything happens all at once.",
           ":a":["Larry", "Moe", "Curly"]
       },
       ReturnValues:"UPDATED_NEW"
   };
   
   console.log("Updating the item...");
   docClient.update(params, function(err, data) {
       if (err) {
           console.error("Unable to update item. Error JSON:", JSON.stringify(err, null, 2));
       } else {
           console.log("UpdateItem succeeded:", JSON.stringify(data, null, 2));
       }
   });
   ```
**Note**  
This program uses `UpdateExpression` to describe all updates you want to perform on the specified item\.  
The `ReturnValues` parameter instructs DynamoDB to return only the updated attributes \(`"UPDATED_NEW"`\)\.

1. To run the program, enter the following command\.

   `node MoviesItemOps03.js`

## Step 3\.4: Increment an Atomic Counter<a name="GettingStarted.NodeJs.03.04"></a>

DynamoDB supports atomic counters, where you use the `update` method to increment or decrement the value of an existing attribute without interfering with other write requests\. \(All write requests are applied in the order in which they are received\.\)

The following program shows how to increment the `rating` for a movie\. Each time you run the program, it increments this attribute by one\.

1. Copy the following program and paste it into a file named `MoviesItemOps04.js`\.

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
   
   var docClient = new AWS.DynamoDB.DocumentClient()
   
   var table = "Movies";
   
   var year = 2015;
   var title = "The Big New Movie";
   
   // Increment an atomic counter
   
   var params = {
       TableName:table,
       Key:{
           "year": year,
           "title": title
       },
       UpdateExpression: "set info.rating = info.rating + :val",
       ExpressionAttributeValues:{
           ":val": 1
       },
       ReturnValues:"UPDATED_NEW"
   };
   
   console.log("Updating the item...");
   docClient.update(params, function(err, data) {
       if (err) {
           console.error("Unable to update item. Error JSON:", JSON.stringify(err, null, 2));
       } else {
           console.log("UpdateItem succeeded:", JSON.stringify(data, null, 2));
       }
   });
   ```

1. To run the program, enter the following command\.

    `node MoviesItemOps04.js` 

## Step 3\.5: Update an Item \(Conditionally\)<a name="GettingStarted.NodeJs.03.05"></a>

The following program shows how to use `UpdateItem` with a condition\. If the condition evaluates to true, the update succeeds; otherwise, the update is not performed\.

In this case, the item is updated only if there are more than three actors in the movie\.

1. Copy the following program and paste it into a file named `MoviesItemOps05.js`\.

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
   
   var docClient = new AWS.DynamoDB.DocumentClient()
   
   var table = "Movies";
   
   var year = 2015;
   var title = "The Big New Movie";
   
   // Conditional update (will fail)
   
   var params = {
       TableName:table,
       Key:{
           "year": year,
           "title": title
       },
       UpdateExpression: "remove info.actors[0]",
       ConditionExpression: "size(info.actors) > :num",
       ExpressionAttributeValues:{
           ":num": 3
       },
       ReturnValues:"UPDATED_NEW"
   };
   
   console.log("Attempting a conditional update...");
   docClient.update(params, function(err, data) {
       if (err) {
           console.error("Unable to update item. Error JSON:", JSON.stringify(err, null, 2));
       } else {
           console.log("UpdateItem succeeded:", JSON.stringify(data, null, 2));
       }
   });
   ```

1. To run the program, enter the following command\.

   `node MoviesItemOps05.js`

   The program should fail with the following message\.

   The conditional request failed

   This is because the movie has three actors in it, but the condition is checking for *greater than* three actors\.

1. Modify the program so that the `ConditionExpression` looks like the following\.

   ```
   ConditionExpression: "size(info.actors) >= :num",
   ```

   The condition is now *greater than or equal to 3* instead of *greater than 3*\.

1. Run the program again\. The `updateItem` operation should now succeed\.

## Step 3\.6: Delete an Item<a name="GettingStarted.NodeJs.03.06"></a>

You can use the `delete` method to delete one item by specifying its primary key\. You can optionally provide a `ConditionExpression` to prevent the item from being deleted if the condition is not met\.

In the following example, you try to delete a specific movie item if its rating is 5 or less\.

1. Copy the following program and paste it into a file named `MoviesItemOps06.js`\.

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
   
   var table = "Movies";
   
   var year = 2015;
   var title = "The Big New Movie";
   
   var params = {
       TableName:table,
       Key:{
           "year": year,
           "title": title
       },
       ConditionExpression:"info.rating <= :val",
       ExpressionAttributeValues: {
           ":val": 5.0
       }
   };
   
   console.log("Attempting a conditional delete...");
   docClient.delete(params, function(err, data) {
       if (err) {
           console.error("Unable to delete item. Error JSON:", JSON.stringify(err, null, 2));
       } else {
           console.log("DeleteItem succeeded:", JSON.stringify(data, null, 2));
       }
   });
   ```

1. To run the program, enter the following command\.

   `node MoviesItemOps06.js`

   The program should fail with the following message\.

   The conditional request failed

   This is because the rating for this particular movie is greater than 5\.

1. Modify the program to remove the condition from `params`\.

   ```
   var params = {
       TableName:table,
       Key:{
           "title":title,
           "year":year
       }
   };
   ```

1. Run the program again\. Now, the delete succeeds because you removed the condition\.