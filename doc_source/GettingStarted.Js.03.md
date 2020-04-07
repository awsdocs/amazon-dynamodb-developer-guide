# Step 3: Create, Read, Update, and Delete an Item<a name="GettingStarted.Js.03"></a>

In this step, you perform read and write operations on an item in the `Movies` table\.

To learn more about reading and writing data, see [Working with Items and Attributes](WorkingWithItems.md)\.

**Topics**
+ [Step 3\.1: Create a New Item](#GettingStarted.Js.03.01)
+ [Step 3\.2: Read an Item](#GettingStarted.Js.03.02)
+ [Step 3\.3: Update an Item](#GettingStarted.Js.03.03)
+ [Step 3\.4: Increment an Atomic Counter](#GettingStarted.Js.03.04)
+ [Step 3\.5: Update an Item \(Conditionally\)](#GettingStarted.Js.03.05)
+ [Step 3\.6: Delete an Item](#GettingStarted.Js.03.06)

## Step 3\.1: Create a New Item<a name="GettingStarted.Js.03.01"></a>

In this step, you add a new item to the `Movies` table\.

1. Copy the following program and paste it into a file named `MoviesItemOps01.html`\.

   ```
   <!-- 
     Copyright 2010-2019 Amazon.com, Inc. or its affiliates. All Rights Reserved.
    
     This file is licensed under the Apache License, Version 2.0 (the "License").
     You may not use this file except in compliance with the License. A copy of
     the License is located at
    
     http://aws.amazon.com/apache2.0/
    
     This file is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR
     CONDITIONS OF ANY KIND, either express or implied. See the License for the
     specific language governing permissions and limitations under the License.
   -->
   <html>
   <head>
   <script src="https://sdk.amazonaws.com/js/aws-sdk-2.7.16.min.js"></script>
   
   <script>
   AWS.config.update({
     region: "us-west-2",
     endpoint: 'http://localhost:8000',
     // accessKeyId default can be used while using the downloadable version of DynamoDB. 
     // For security reasons, do not store AWS Credentials in your files. Use Amazon Cognito instead.
     accessKeyId: "fakeMyKeyId",
     // secretAccessKey default can be used while using the downloadable version of DynamoDB. 
     // For security reasons, do not store AWS Credentials in your files. Use Amazon Cognito instead.
     secretAccessKey: "fakeSecretAccessKey"
   });
   
   var docClient = new AWS.DynamoDB.DocumentClient();
   
   function createItem() {
       var params = {
           TableName :"Movies",
           Item:{
               "year": 2015,
               "title": "The Big New Movie",
               "info":{
                   "plot": "Nothing happens at all.",
                   "rating": 0
               }
           }
       };
       docClient.put(params, function(err, data) {
           if (err) {
               document.getElementById('textarea').innerHTML = "Unable to add item: " + "\n" + JSON.stringify(err, undefined, 2);
           } else {
               document.getElementById('textarea').innerHTML = "PutItem succeeded: " + "\n" + JSON.stringify(data, undefined, 2);
           }
       });
   }
   
   </script>
   </head>
   
   <body>
   <input id="createItem" type="button" value="Create Item" onclick="createItem();" />
   <br><br>
   <textarea readonly id= "textarea" style="width:400px; height:800px"></textarea>
   
   </body>
   </html>
   ```
**Note**  
The primary key is required\. This code adds an item that has a primary key \(`year`, `title`\) and `info` attributes\. The `info` attribute stores sample JSON that provides more information about the movie\.

1. Open the `MoviesItemOps01.html` file in your browser\.

1. Choose **Create Item**\.

## Step 3\.2: Read an Item<a name="GettingStarted.Js.03.02"></a>

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

You can use the `get` method to read the item from the `Movies` table\. You must specify the primary key values, so you can read any item from `Movies` if you know its `year` and `title`\. 

1. Copy the following program and paste it into a file named `MoviesItemOps02.html`\.

   ```
   <!-- 
     Copyright 2010-2019 Amazon.com, Inc. or its affiliates. All Rights Reserved.
    
     This file is licensed under the Apache License, Version 2.0 (the "License").
     You may not use this file except in compliance with the License. A copy of
     the License is located at
    
     http://aws.amazon.com/apache2.0/
    
     This file is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR
     CONDITIONS OF ANY KIND, either express or implied. See the License for the
     specific language governing permissions and limitations under the License.
   -->
   <html>
   <head>
   <script src="https://sdk.amazonaws.com/js/aws-sdk-2.7.16.min.js"></script>
   
   <script>
   AWS.config.update({
     region: "us-west-2",
     endpoint: 'http://localhost:8000',
     // accessKeyId default can be used while using the downloadable version of DynamoDB. 
     // For security reasons, do not store AWS Credentials in your files. Use Amazon Cognito instead.
     accessKeyId: "fakeMyKeyId",
     // secretAccessKey default can be used while using the downloadable version of DynamoDB. 
     // For security reasons, do not store AWS Credentials in your files. Use Amazon Cognito instead.
     secretAccessKey: "fakeSecretAccessKey"
   });
   
   var docClient = new AWS.DynamoDB.DocumentClient();
   
   function readItem() {
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
               document.getElementById('textarea').innerHTML = "Unable to read item: " + "\n" + JSON.stringify(err, undefined, 2);
           } else {
               document.getElementById('textarea').innerHTML = "GetItem succeeded: " + "\n" + JSON.stringify(data, undefined, 2);
           }
       });
   }
   
   </script>
   </head>
   
   <body>
   <input id="readItem" type="button" value="Read Item" onclick="readItem();" />
   <br><br>
   <textarea readonly id= "textarea" style="width:400px; height:800px"></textarea>
   
   </body>
   </html>
   ```

1. Open the `MoviesItemOps02.html` file in your browser\.

1. Choose **Read Item**\.

## Step 3\.3: Update an Item<a name="GettingStarted.Js.03.03"></a>

You can use the `update` method to modify an item\. You can update values of existing attributes, add new attributes, or remove attributes\.

In this example, you perform the following updates:
+ Change the value of the existing attributes \(`rating`, `plot`\)\. 
+ Add a new list attribute \(`actors`\) to the existing `info` map\.

The item changes from the following:

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

To this:

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

1. Copy the following program and paste it into a file named `MoviesItemOps03.html`\.

   ```
   <!-- 
     Copyright 2010-2019 Amazon.com, Inc. or its affiliates. All Rights Reserved.
    
     This file is licensed under the Apache License, Version 2.0 (the "License").
     You may not use this file except in compliance with the License. A copy of
     the License is located at
    
     http://aws.amazon.com/apache2.0/
    
     This file is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR
     CONDITIONS OF ANY KIND, either express or implied. See the License for the
     specific language governing permissions and limitations under the License.
   -->
   <html>
   <head>
   <script src="https://sdk.amazonaws.com/js/aws-sdk-2.7.16.min.js"></script>
   
   <script>
   AWS.config.update({
     region: "us-west-2",
     endpoint: 'http://localhost:8000',
     // accessKeyId default can be used while using the downloadable version of DynamoDB. 
     // For security reasons, do not store AWS Credentials in your files. Use Amazon Cognito instead.
     accessKeyId: "fakeMyKeyId",
     // secretAccessKey default can be used while using the downloadable version of DynamoDB. 
     // For security reasons, do not store AWS Credentials in your files. Use Amazon Cognito instead.
     secretAccessKey: "fakeSecretAccessKey"
   });
   
   var docClient = new AWS.DynamoDB.DocumentClient();
   
   function updateItem() {
       var table = "Movies";
       var year = 2015;
       var title = "The Big New Movie";
   
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
   
       docClient.update(params, function(err, data) {
           if (err) {
               document.getElementById('textarea').innerHTML = "Unable to update item: " + "\n" + JSON.stringify(err, undefined, 2);
           } else {
               document.getElementById('textarea').innerHTML = "UpdateItem succeeded: " + "\n" + JSON.stringify(data, undefined, 2);
           }
       });
   }
   
   </script>
   </head>
   
   <body>
   <input id="updateItem" type="button" value="Update Item" onclick="updateItem();" />
   <br><br>
   <textarea readonly id= "textarea" style="width:400px; height:800px"></textarea>
   
   </body>
   </html>
   ```
**Note**  
This program uses `UpdateExpression` to describe all updates you want to perform on the specified item\.  
The `ReturnValues` parameter instructs Amazon DynamoDB to return only the updated attributes \(`"UPDATED_NEW"`\)\.

1. Open the `MoviesItemOps03.html` file in your browser\.

1. Choose **Update Item**\.

## Step 3\.4: Increment an Atomic Counter<a name="GettingStarted.Js.03.04"></a>

DynamoDB supports atomic counters, where you use the `update` method to increment or decrement the value of an attribute without interfering with other write requests\. \(All write requests are applied in the order in which they are received\.\)

The following program shows how to increment the `rating` for a movie\. Each time you run it, the program increments this attribute by one\. 

1. Copy the following program and paste it into a file named `MoviesItemOps04.html`\.

   ```
   <!-- 
     Copyright 2010-2019 Amazon.com, Inc. or its affiliates. All Rights Reserved.
    
     This file is licensed under the Apache License, Version 2.0 (the "License").
     You may not use this file except in compliance with the License. A copy of
     the License is located at
    
     http://aws.amazon.com/apache2.0/
    
     This file is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR
     CONDITIONS OF ANY KIND, either express or implied. See the License for the
     specific language governing permissions and limitations under the License.
   -->
   <html>
   <head>
   <script src="https://sdk.amazonaws.com/js/aws-sdk-2.7.16.min.js"></script>
   
   <script>
   AWS.config.update({
     region: "us-west-2",
     endpoint: 'http://localhost:8000',
     // accessKeyId default can be used while using the downloadable version of DynamoDB. 
     // For security reasons, do not store AWS Credentials in your files. Use Amazon Cognito instead.
     accessKeyId: "fakeMyKeyId",
     // secretAccessKey default can be used while using the downloadable version of DynamoDB. 
     // For security reasons, do not store AWS Credentials in your files. Use Amazon Cognito instead.
     secretAccessKey: "fakeSecretAccessKey"
   });
   
   var docClient = new AWS.DynamoDB.DocumentClient();
   
   function increaseRating() {
       var table = "Movies";
       var year = 2015;
       var title = "The Big New Movie";
   
       var params = {
           TableName:table,
           Key:{
               "year": year,
               "title": title
           },
           UpdateExpression: "set info.rating = info.rating + :val",
           ExpressionAttributeValues:{
               ":val":1
           },
           ReturnValues:"UPDATED_NEW"
       };
   
       docClient.update(params, function(err, data) {
           if (err) {
               document.getElementById('textarea').innerHTML = "Unable to update rating: " + "\n" + JSON.stringify(err, undefined, 2);
           } else {
               document.getElementById('textarea').innerHTML = "Increase Rating succeeded: " + "\n" + JSON.stringify(data, undefined, 2);
           }
       });
   }
   
   </script>
   </head>
   
   <body>
   <input id="increaseRating" type="button" value="Increase Rating" onclick="increaseRating();" />
   <br><br>
   <textarea readonly id= "textarea" style="width:400px; height:800px"></textarea>
   
   </body>
   </html>
   ```

1. Open the `MoviesItemOps04.html` file in your browser\.

1. Choose **Increase Rating**\.

## Step 3\.5: Update an Item \(Conditionally\)<a name="GettingStarted.Js.03.05"></a>

The following program shows how to use `UpdateItem` with a condition\. If the condition evaluates to true, the update succeeds; otherwise, the update is not performed\.

In this case, the item is updated only if there are more than three actors in the movie\.

1. Copy the following program and paste it into a file named `MoviesItemOps05.html`\.

   ```
   <!-- 
     Copyright 2010-2019 Amazon.com, Inc. or its affiliates. All Rights Reserved.
    
     This file is licensed under the Apache License, Version 2.0 (the "License").
     You may not use this file except in compliance with the License. A copy of
     the License is located at
    
     http://aws.amazon.com/apache2.0/
    
     This file is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR
     CONDITIONS OF ANY KIND, either express or implied. See the License for the
     specific language governing permissions and limitations under the License.
   -->
   <html>
   <head>
   <script src="https://sdk.amazonaws.com/js/aws-sdk-2.7.16.min.js"></script>
   
   <script>
   AWS.config.update({
     region: "us-west-2",
     endpoint: 'http://localhost:8000',
     // accessKeyId default can be used while using the downloadable version of DynamoDB. 
     // For security reasons, do not store AWS Credentials in your files. Use Amazon Cognito instead.
     accessKeyId: "fakeMyKeyId",
     // secretAccessKey default can be used while using the downloadable version of DynamoDB. 
     // For security reasons, do not store AWS Credentials in your files. Use Amazon Cognito instead.
     secretAccessKey: "fakeSecretAccessKey"
   });
   
   var docClient = new AWS.DynamoDB.DocumentClient();
   
   function conditionalUpdate() {
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
               ":num":3
           },
           ReturnValues:"UPDATED_NEW"
       };
   
       docClient.update(params, function(err, data) {
           if (err) {
               document.getElementById('textarea').innerHTML = "The conditional update failed: " + "\n" + JSON.stringify(err, undefined, 2);
           } else {
               document.getElementById('textarea').innerHTML = "The conditional update succeeded: " + "\n" + JSON.stringify(data, undefined, 2);
           }
       });
   }
   
   </script>
   </head>
   
   <body>
   <input id="conditionalUpdate" type="button" value="Conditional Update" onclick="conditionalUpdate();" />
   <br><br>
   <textarea readonly id= "textarea" style="width:400px; height:800px"></textarea>
   
   </body>
   </html>
   ```

1. Open the `MoviesItemOps05.html` file in your browser\.

1. Choose **Conditional Update**\.

   The program should fail with the following message:

   The conditional update failed

   This is because the movie has three actors in it, but the condition is checking for *greater than* three actors\.

1. Modify the program so that the `ConditionExpression` looks like the following\.

   ```
   ConditionExpression: "size(info.actors) >= :num", 
   ```

   The condition is now *greater than or equal to 3* instead of *greater than 3*\.

1. Run the program again\. The `updateItem` operation should now succeed\.

## Step 3\.6: Delete an Item<a name="GettingStarted.Js.03.06"></a>

You can use the `delete` method to delete one item by specifying its primary key\. You can optionally provide a `ConditionExpression` to prevent the item from being deleted if the condition is not met\.

In the following example, you try to delete a specific movie item if its rating is 5 or less\.

1. Copy the following program and paste it into a file named `MoviesItemOps06.html`\.

   ```
   <!-- 
     Copyright 2010-2019 Amazon.com, Inc. or its affiliates. All Rights Reserved.
    
     This file is licensed under the Apache License, Version 2.0 (the "License").
     You may not use this file except in compliance with the License. A copy of
     the License is located at
    
     http://aws.amazon.com/apache2.0/
    
     This file is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR
     CONDITIONS OF ANY KIND, either express or implied. See the License for the
     specific language governing permissions and limitations under the License.
   -->
   <html>
   <head>
   <script src="https://sdk.amazonaws.com/js/aws-sdk-2.7.16.min.js"></script>
   
   <script>
   AWS.config.update({
     region: "us-west-2",
     endpoint: 'http://localhost:8000',
     // accessKeyId default can be used while using the downloadable version of DynamoDB. 
     // For security reasons, do not store AWS Credentials in your files. Use Amazon Cognito instead.
     accessKeyId: "fakeMyKeyId",
     // secretAccessKey default can be used while using the downloadable version of DynamoDB. 
     // For security reasons, do not store AWS Credentials in your files. Use Amazon Cognito instead.
     secretAccessKey: "fakeSecretAccessKey"
   });
   
   var docClient = new AWS.DynamoDB.DocumentClient();
   
   function conditionalDelete() {
       var table = "Movies";
       var year = 2015;
       var title = "The Big New Movie";
   
       var params = {
           TableName:table,
           Key:{
               "year":year,
               "title":title
           },
           ConditionExpression:"info.rating <= :val",
           ExpressionAttributeValues: {
               ":val": 5.0
           }
       };
   
       docClient.delete(params, function(err, data) {
           if (err) {
               document.getElementById('textarea').innerHTML = "The conditional delete failed: " + "\n" + JSON.stringify(err, undefined, 2);
           } else {
               document.getElementById('textarea').innerHTML = "The conditional delete succeeded: " + "\n" + JSON.stringify(data, undefined, 2);
           }
       });
   }
   
   </script>
   </head>
   
   <body>
   <input id="conditionalDelete" type="button" value="Conditional Delete" onclick="conditionalDelete();" />
   <br><br>
   <textarea readonly id= "textarea" style="width:400px; height:800px"></textarea>
   
   </body>
   </html>
   ```

1. Open the `MoviesItemOps06.html` file in your browser\.

1. Choose **Conditional Delete**\.

   The program should fail with the following message:

   The conditional delete failed

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

1. Run the program again\. The delete succeeds because you removed the condition\.