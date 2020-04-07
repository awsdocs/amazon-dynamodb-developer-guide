# Step 3: Create, Read, Update, and Delete an Item<a name="GettingStarted.Java.03"></a>

In this step, you perform read and write operations on an item in the `Movies` table\.

To learn more about reading and writing data, see [Working with Items and Attributes](WorkingWithItems.md)\.

**Topics**
+ [Step 3\.1: Create a New Item](#GettingStarted.Java.03.01)
+ [Step 3\.2: Read an Item](#GettingStarted.Java.03.02)
+ [Step 3\.3: Update an Item](#GettingStarted.Java.03.03)
+ [Step 3\.4: Increment an Atomic Counter](#GettingStarted.Java.03.04)
+ [Step 3\.5: Update an Item \(Conditionally\)](#GettingStarted.Java.03.05)
+ [Step 3\.6: Delete an Item](#GettingStarted.Java.03.06)

## Step 3\.1: Create a New Item<a name="GettingStarted.Java.03.01"></a>

In this step, you add a new item to the `Movies` table\.

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
   import java.util.Map;
   
   import com.amazonaws.client.builder.AwsClientBuilder;
   import com.amazonaws.services.dynamodbv2.AmazonDynamoDB;
   import com.amazonaws.services.dynamodbv2.AmazonDynamoDBClientBuilder;
   import com.amazonaws.services.dynamodbv2.document.DynamoDB;
   import com.amazonaws.services.dynamodbv2.document.Item;
   import com.amazonaws.services.dynamodbv2.document.PutItemOutcome;
   import com.amazonaws.services.dynamodbv2.document.Table;
   
   public class MoviesItemOps01 {
   
       public static void main(String[] args) throws Exception {
   
           AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard()
               .withEndpointConfiguration(new AwsClientBuilder.EndpointConfiguration("http://localhost:8000", "us-west-2"))
               .build();
   
           DynamoDB dynamoDB = new DynamoDB(client);
   
           Table table = dynamoDB.getTable("Movies");
   
           int year = 2015;
           String title = "The Big New Movie";
   
           final Map<String, Object> infoMap = new HashMap<String, Object>();
           infoMap.put("plot", "Nothing happens at all.");
           infoMap.put("rating", 0);
   
           try {
               System.out.println("Adding a new item...");
               PutItemOutcome outcome = table
                   .putItem(new Item().withPrimaryKey("year", year, "title", title).withMap("info", infoMap));
   
               System.out.println("PutItem succeeded:\n" + outcome.getPutItemResult());
   
           }
           catch (Exception e) {
               System.err.println("Unable to add item: " + year + " " + title);
               System.err.println(e.getMessage());
           }
   
       }
   }
   ```
**Note**  
The primary key is required\. This code adds an item that has primary key \(`year`, `title`\) and `info` attributes\. The `info` attribute stores JSON example code that provides more information about the movie\.

1. Compile and run the program\.

## Step 3\.2: Read an Item<a name="GettingStarted.Java.03.02"></a>

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

You can use the `getItem` method to read the item from the `Movies` table\. You must specify the primary key values so that you can read any item from `Movies` if you know its `year` and `title`\.

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
   
   import com.amazonaws.client.builder.AwsClientBuilder;
   import com.amazonaws.services.dynamodbv2.AmazonDynamoDB;
   import com.amazonaws.services.dynamodbv2.AmazonDynamoDBClientBuilder;
   import com.amazonaws.services.dynamodbv2.document.DynamoDB;
   import com.amazonaws.services.dynamodbv2.document.Item;
   import com.amazonaws.services.dynamodbv2.document.Table;
   import com.amazonaws.services.dynamodbv2.document.spec.GetItemSpec;
   
   public class MoviesItemOps02 {
   
       public static void main(String[] args) throws Exception {
   
           AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard()
               .withEndpointConfiguration(new AwsClientBuilder.EndpointConfiguration("http://localhost:8000", "us-west-2"))
               .build();
   
           DynamoDB dynamoDB = new DynamoDB(client);
   
           Table table = dynamoDB.getTable("Movies");
   
           int year = 2015;
           String title = "The Big New Movie";
   
           GetItemSpec spec = new GetItemSpec().withPrimaryKey("year", year, "title", title);
   
           try {
               System.out.println("Attempting to read the item...");
               Item outcome = table.getItem(spec);
               System.out.println("GetItem succeeded: " + outcome);
   
           }
           catch (Exception e) {
               System.err.println("Unable to read item: " + year + " " + title);
               System.err.println(e.getMessage());
           }
   
       }
   }
   ```

1. Compile and run the program\.

## Step 3\.3: Update an Item<a name="GettingStarted.Java.03.03"></a>

You can use the `updateItem` method to modify an existing item\. You can update values of existing attributes, add new attributes, or remove attributes\.

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
   
   import java.util.Arrays;
   
   import com.amazonaws.client.builder.AwsClientBuilder;
   import com.amazonaws.services.dynamodbv2.AmazonDynamoDB;
   import com.amazonaws.services.dynamodbv2.AmazonDynamoDBClientBuilder;
   import com.amazonaws.services.dynamodbv2.document.DynamoDB;
   import com.amazonaws.services.dynamodbv2.document.Table;
   import com.amazonaws.services.dynamodbv2.document.UpdateItemOutcome;
   import com.amazonaws.services.dynamodbv2.document.spec.UpdateItemSpec;
   import com.amazonaws.services.dynamodbv2.document.utils.ValueMap;
   import com.amazonaws.services.dynamodbv2.model.ReturnValue;
   
   public class MoviesItemOps03 {
   
       public static void main(String[] args) throws Exception {
   
           AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard()
               .withEndpointConfiguration(new AwsClientBuilder.EndpointConfiguration("http://localhost:8000", "us-west-2"))
               .build();
   
           DynamoDB dynamoDB = new DynamoDB(client);
   
           Table table = dynamoDB.getTable("Movies");
   
           int year = 2015;
           String title = "The Big New Movie";
   
           UpdateItemSpec updateItemSpec = new UpdateItemSpec().withPrimaryKey("year", year, "title", title)
               .withUpdateExpression("set info.rating = :r, info.plot=:p, info.actors=:a")
               .withValueMap(new ValueMap().withNumber(":r", 5.5).withString(":p", "Everything happens all at once.")
                   .withList(":a", Arrays.asList("Larry", "Moe", "Curly")))
               .withReturnValues(ReturnValue.UPDATED_NEW);
   
           try {
               System.out.println("Updating the item...");
               UpdateItemOutcome outcome = table.updateItem(updateItemSpec);
               System.out.println("UpdateItem succeeded:\n" + outcome.getItem().toJSONPretty());
   
           }
           catch (Exception e) {
               System.err.println("Unable to update item: " + year + " " + title);
               System.err.println(e.getMessage());
           }
       }
   }
   ```
**Note**  
This program uses an `UpdateExpression` to describe all updates you want to perform on the specified item\.  
The `ReturnValues` parameter instructs Amazon DynamoDB to return only the updated attributes \(`UPDATED_NEW`\)\.

1. Compile and run the program\.

## Step 3\.4: Increment an Atomic Counter<a name="GettingStarted.Java.03.04"></a>

DynamoDB supports atomic counters\. Use the `updateItem` method to increment or decrement the value of an existing attribute without interfering with other write requests\. \(All write requests are applied in the order in which they are received\.\) 

The following program shows how to increment the `rating` for a movie\. Each time you run it, the program increments this attribute by one\.

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
   
   import com.amazonaws.client.builder.AwsClientBuilder;
   import com.amazonaws.services.dynamodbv2.AmazonDynamoDB;
   import com.amazonaws.services.dynamodbv2.AmazonDynamoDBClientBuilder;
   import com.amazonaws.services.dynamodbv2.document.DynamoDB;
   import com.amazonaws.services.dynamodbv2.document.Table;
   import com.amazonaws.services.dynamodbv2.document.UpdateItemOutcome;
   import com.amazonaws.services.dynamodbv2.document.spec.UpdateItemSpec;
   import com.amazonaws.services.dynamodbv2.document.utils.ValueMap;
   import com.amazonaws.services.dynamodbv2.model.ReturnValue;
   
   public class MoviesItemOps04 {
   
       public static void main(String[] args) throws Exception {
   
           AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard()
               .withEndpointConfiguration(new AwsClientBuilder.EndpointConfiguration("http://localhost:8000", "us-west-2"))
               .build();
   
           DynamoDB dynamoDB = new DynamoDB(client);
   
           Table table = dynamoDB.getTable("Movies");
   
           int year = 2015;
           String title = "The Big New Movie";
   
           UpdateItemSpec updateItemSpec = new UpdateItemSpec().withPrimaryKey("year", year, "title", title)
               .withUpdateExpression("set info.rating = info.rating + :val")
               .withValueMap(new ValueMap().withNumber(":val", 1)).withReturnValues(ReturnValue.UPDATED_NEW);
   
           try {
               System.out.println("Incrementing an atomic counter...");
               UpdateItemOutcome outcome = table.updateItem(updateItemSpec);
               System.out.println("UpdateItem succeeded:\n" + outcome.getItem().toJSONPretty());
   
           }
           catch (Exception e) {
               System.err.println("Unable to update item: " + year + " " + title);
               System.err.println(e.getMessage());
           }
       }
   }
   ```

1. Compile and run the program\.

## Step 3\.5: Update an Item \(Conditionally\)<a name="GettingStarted.Java.03.05"></a>

The following program shows how to use `UpdateItem` with a condition\. If the condition evaluates to true, the update succeeds; otherwise, the update is not performed\.

In this case, the movie item is updated only if there are more than three actors\.

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
   
   import com.amazonaws.client.builder.AwsClientBuilder;
   import com.amazonaws.services.dynamodbv2.AmazonDynamoDB;
   import com.amazonaws.services.dynamodbv2.AmazonDynamoDBClientBuilder;
   import com.amazonaws.services.dynamodbv2.document.DynamoDB;
   import com.amazonaws.services.dynamodbv2.document.PrimaryKey;
   import com.amazonaws.services.dynamodbv2.document.Table;
   import com.amazonaws.services.dynamodbv2.document.UpdateItemOutcome;
   import com.amazonaws.services.dynamodbv2.document.spec.UpdateItemSpec;
   import com.amazonaws.services.dynamodbv2.document.utils.ValueMap;
   import com.amazonaws.services.dynamodbv2.model.ReturnValue;
   
   public class MoviesItemOps05 {
   
       public static void main(String[] args) throws Exception {
   
           AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard()
               .withEndpointConfiguration(new AwsClientBuilder.EndpointConfiguration("http://localhost:8000", "us-west-2"))
               .build();
   
           DynamoDB dynamoDB = new DynamoDB(client);
   
           Table table = dynamoDB.getTable("Movies");
   
           int year = 2015;
           String title = "The Big New Movie";
   
           UpdateItemSpec updateItemSpec = new UpdateItemSpec()
               .withPrimaryKey(new PrimaryKey("year", year, "title", title)).withUpdateExpression("remove info.actors[0]")
               .withConditionExpression("size(info.actors) > :num").withValueMap(new ValueMap().withNumber(":num", 3))
               .withReturnValues(ReturnValue.UPDATED_NEW);
   
           // Conditional update (we expect this to fail)
           try {
               System.out.println("Attempting a conditional update...");
               UpdateItemOutcome outcome = table.updateItem(updateItemSpec);
               System.out.println("UpdateItem succeeded:\n" + outcome.getItem().toJSONPretty());
   
           }
           catch (Exception e) {
               System.err.println("Unable to update item: " + year + " " + title);
               System.err.println(e.getMessage());
           }
       }
   }
   ```

1. Compile and run the program\.

   The program should fail with the following message:

   The conditional request failed

   This is because the movie has three actors in it, but the condition is checking for *greater than* three actors\.

1. Modify the program so that the `ConditionExpression` looks like the following\.

   ```
   .withConditionExpression("size(info.actors) >= :num")
   ```

   The condition is now *greater than or equal to 3* instead of *greater than 3*\.

1. Compile and run the program\. The `UpdateItem` operation should now succeed\.

## Step 3\.6: Delete an Item<a name="GettingStarted.Java.03.06"></a>

You can use the `deleteItem` method to delete one item by specifying its primary key\. You can optionally provide a `ConditionExpression` to prevent the item from being deleted if the condition is not met\.

In the following example, you try to delete a specific movie item if its rating is 5 or less\.

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
   
   import com.amazonaws.client.builder.AwsClientBuilder;
   import com.amazonaws.services.dynamodbv2.AmazonDynamoDB;
   import com.amazonaws.services.dynamodbv2.AmazonDynamoDBClientBuilder;
   import com.amazonaws.services.dynamodbv2.document.DynamoDB;
   import com.amazonaws.services.dynamodbv2.document.PrimaryKey;
   import com.amazonaws.services.dynamodbv2.document.Table;
   import com.amazonaws.services.dynamodbv2.document.spec.DeleteItemSpec;
   import com.amazonaws.services.dynamodbv2.document.utils.ValueMap;
   
   public class MoviesItemOps06 {
   
       public static void main(String[] args) throws Exception {
   
           AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard()
               .withEndpointConfiguration(new AwsClientBuilder.EndpointConfiguration("http://localhost:8000", "us-west-2"))
               .build();
   
           DynamoDB dynamoDB = new DynamoDB(client);
   
           Table table = dynamoDB.getTable("Movies");
   
           int year = 2015;
           String title = "The Big New Movie";
   
           DeleteItemSpec deleteItemSpec = new DeleteItemSpec()
               .withPrimaryKey(new PrimaryKey("year", year, "title", title)).withConditionExpression("info.rating <= :val")
               .withValueMap(new ValueMap().withNumber(":val", 5.0));
   
           // Conditional delete (we expect this to fail)
   
           try {
               System.out.println("Attempting a conditional delete...");
               table.deleteItem(deleteItemSpec);
               System.out.println("DeleteItem succeeded");
           }
           catch (Exception e) {
               System.err.println("Unable to delete item: " + year + " " + title);
               System.err.println(e.getMessage());
           }
       }
   }
   ```

1. Compile and run the program\.

   The program should fail with the following message:

   The conditional request failed

   This is because the rating for this particular move is greater than 5\.

1. Modify the program to remove the condition in `DeleteItemSpec`\.

   ```
   DeleteItemSpec deleteItemSpec = new DeleteItemSpec()
   .withPrimaryKey(new PrimaryKey("year", 2015, "title", "The Big New Movie"));
   ```

1. Compile and run the program\. Now, the delete succeeds because you removed the condition\.