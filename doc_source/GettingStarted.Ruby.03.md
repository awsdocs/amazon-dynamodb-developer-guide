# Step 3: Create, Read, Update, and Delete an Item<a name="GettingStarted.Ruby.03"></a>

In this step, you perform read and write operations on an item in the `Movies` table in Amazon DynamoDB\.

To learn more about reading and writing data, see [Working with Items and Attributes](WorkingWithItems.md)\.

**Topics**
+ [Step 3\.1: Create a New Item](#GettingStarted.Ruby.03.01)
+ [Step 3\.2: Read an Item](#GettingStarted.Ruby.03.02)
+ [Step 3\.3: Update an Item](#GettingStarted.Ruby.03.03)
+ [Step 3\.4: Increment an Atomic Counter](#GettingStarted.Ruby.03.04)
+ [Step 3\.5: Update an Item \(Conditionally\)](#GettingStarted.Ruby.03.05)
+ [Step 3\.6: Delete an Item](#GettingStarted.Ruby.03.06)

## Step 3\.1: Create a New Item<a name="GettingStarted.Ruby.03.01"></a>

In this step, you add a new item to the table\.

1. Copy the following program and paste it into a file named `MoviesItemOps01.rb`\.

   ```
   #
   #  Copyright 2010-2019 Amazon.com, Inc. or its affiliates. All Rights Reserved.
   #
   #  This file is licensed under the Apache License, Version 2.0 (the "License").
   #  You may not use this file except in compliance with the License. A copy of
   #  the License is located at
   # 
   #  http://aws.amazon.com/apache2.0/
   # 
   #  This file is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR
   #  CONDITIONS OF ANY KIND, either express or implied. See the License for the
   #  specific language governing permissions and limitations under the License.
   #
   require "aws-sdk"
   
   Aws.config.update({
     region: "us-west-2",
     endpoint: "http://localhost:8000"
   })
   
   dynamodb = Aws::DynamoDB::Client.new
   
   table_name = 'Movies'
   
   year = 2015
   title = "The Big New Movie"
   
   item = {
       year: year,
       title: title,
       info: {
               plot: "Nothing happens at all.",
               rating: 0
       }
   }
   
   params = {
       table_name: table_name,
       item: item
   }
   
   begin
       dynamodb.put_item(params)
       puts "Added item: #{year}  - #{title}"
   
   rescue  Aws::DynamoDB::Errors::ServiceError => error
       puts "Unable to add item:"
       puts "#{error.message}"
   end
   ```
**Note**  
The primary key is required\. This code adds an item that has primary key \(`year`, `title`\) and `info` attributes\. The `info` attribute stores a map that provides more information about the movie\.

1. To run the program, enter the following command\.

   `ruby MoviesItemOps01.rb`

## Step 3\.2: Read an Item<a name="GettingStarted.Ruby.03.02"></a>

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

You can use the `get_item` method to read the item from the `Movies` table\. You must specify the primary key values so that you can read any item from `Movies` if you know its `year` and `title`\.

1. Copy the following program and paste it into a file named `MoviesItemOps02.rb`\.

   ```
   #
   #  Copyright 2010-2019 Amazon.com, Inc. or its affiliates. All Rights Reserved.
   #
   #  This file is licensed under the Apache License, Version 2.0 (the "License").
   #  You may not use this file except in compliance with the License. A copy of
   #  the License is located at
   # 
   #  http://aws.amazon.com/apache2.0/
   # 
   #  This file is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR
   #  CONDITIONS OF ANY KIND, either express or implied. See the License for the
   #  specific language governing permissions and limitations under the License.
   #
   require "aws-sdk"
   
   Aws.config.update({
     region: "us-west-2",
     endpoint: "http://localhost:8000"
   })
   
   dynamodb = Aws::DynamoDB::Client.new
   
   table_name = 'Movies'
   
   year = 2015
   title = "The Big New Movie"
   
   params = {
       table_name: table_name,
       key: {
           year: year,
           title: title
       }
   }
   
   begin
       result = dynamodb.get_item(params)
       printf "%i - %s\n%s\n%d\n", 
           result.item["year"],
           result.item["title"],
           result.item["info"]["plot"],
           result.item["info"]["rating"]
   
   rescue  Aws::DynamoDB::Errors::ServiceError => error
       puts "Unable to read item:"
       puts "#{error.message}"
   end
   ```

1. To run the program, enter the following command\.

   `ruby MoviesItemOps02.rb`

## Step 3\.3: Update an Item<a name="GettingStarted.Ruby.03.03"></a>

You can use the `update_item` method to modify an existing item\. You can update values of existing attributes, add new attributes, or remove attributes\.

In this example, you perform the following updates:
+ Change the value of the existing attributes \(`rating`, `plot`\)\. 
+ Add a new list attribute \(`actors`\) to the existing `info` map\.

The following shows the existing item\.

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

1. Copy the following program and paste it into a file named `MoviesItemOps03.rb`\.

   ```
   #
   #  Copyright 2010-2019 Amazon.com, Inc. or its affiliates. All Rights Reserved.
   #
   #  This file is licensed under the Apache License, Version 2.0 (the "License").
   #  You may not use this file except in compliance with the License. A copy of
   #  the License is located at
   # 
   #  http://aws.amazon.com/apache2.0/
   # 
   #  This file is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR
   #  CONDITIONS OF ANY KIND, either express or implied. See the License for the
   #  specific language governing permissions and limitations under the License.
   #
   require "aws-sdk"
   
   Aws.config.update({
     region: "us-west-2",
     endpoint: "http://localhost:8000"
   })
   
   dynamodb = Aws::DynamoDB::Client.new
   
   table_name = 'Movies'
   
   year = 2015
   title = "The Big New Movie"
   
   params = {
       table_name: table_name,
       key: {
           year: year,
           title: title
       },
       update_expression: "set info.rating = :r, info.plot=:p, info.actors=:a",
       expression_attribute_values: {
           ":r" => 5.5,
           ":p" => "Everything happens all at once.", # value <Hash,Array,String,Numeric,Boolean,IO,Set,nil>
           ":a" => ["Larry", "Moe", "Curly"]
       },
       return_values: "UPDATED_NEW"
   }
   
   begin
       dynamodb.update_item(params)
       puts "Added item: #{year}  - #{title}"
   
   rescue  Aws::DynamoDB::Errors::ServiceError => error
       puts "Unable to add item:"
       puts "#{error.message}"
   end
   ```
**Note**  
This program uses `update_expression` to describe all updates you want to perform on the specified item\.  
The `return_values` parameter instructs Amazon DynamoDB to return only the updated attributes \(`UPDATED_NEW`\)\.

1. To run the program, enter the following command\.

   `ruby MoviesItemOps03.rb`

## Step 3\.4: Increment an Atomic Counter<a name="GettingStarted.Ruby.03.04"></a>

DynamoDB supports atomic counters, which use the `update_item` method to increment or decrement the value of an existing attribute without interfering with other write requests\. \(All write requests are applied in the order in which they are received\.\)

The following program shows how to increment the `rating` for a movie\. Each time you run the program, it increments this attribute by one\. 

1. Copy the following program and paste it into a file named `MoviesItemOps04.rb`\.

   ```
   #
   #  Copyright 2010-2019 Amazon.com, Inc. or its affiliates. All Rights Reserved.
   #
   #  This file is licensed under the Apache License, Version 2.0 (the "License").
   #  You may not use this file except in compliance with the License. A copy of
   #  the License is located at
   # 
   #  http://aws.amazon.com/apache2.0/
   # 
   #  This file is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR
   #  CONDITIONS OF ANY KIND, either express or implied. See the License for the
   #  specific language governing permissions and limitations under the License.
   #
   require "aws-sdk"
   
   Aws.config.update({
     region: "us-west-2",
     endpoint: "http://localhost:8000"
   })
   
   dynamodb = Aws::DynamoDB::Client.new
   
   table_name = 'Movies'
   
   year = 2015
   title = "The Big New Movie"
   
   params = {
       table_name: table_name,
       key: {
           year: year,
           title: title
       },
       update_expression: "set info.rating = info.rating + :val",
       expression_attribute_values: {
           ":val" => 1
       },
       return_values: "UPDATED_NEW"
   }
   
   begin
       result = dynamodb.update_item(params)
       puts "Updated item. ReturnValues are:"
       result.attributes["info"].each do |key, value| 
           if key == "rating" 
               puts "#{key}: #{value.to_f}" 
           else
               puts "#{key}: #{value}" 
           end
       end
   rescue  Aws::DynamoDB::Errors::ServiceError => error
       puts "Unable to update item:"
       puts "#{error.message}"
   end
   ```

1. To run the program, enter the following command\.

   `ruby MoviesItemOps04.rb`

## Step 3\.5: Update an Item \(Conditionally\)<a name="GettingStarted.Ruby.03.05"></a>

The following program shows how to use `update_item` with a condition\. If the condition evaluates to true, the update succeeds; otherwise, the update is not performed\. 

In this case, the item is updated only if the number of actors is greater than three\.

1. Copy the following program and paste it into a file named `MoviesItemOps05.rb`\.

   ```
   #
   #  Copyright 2010-2019 Amazon.com, Inc. or its affiliates. All Rights Reserved.
   #
   #  This file is licensed under the Apache License, Version 2.0 (the "License").
   #  You may not use this file except in compliance with the License. A copy of
   #  the License is located at
   # 
   #  http://aws.amazon.com/apache2.0/
   # 
   #  This file is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR
   #  CONDITIONS OF ANY KIND, either express or implied. See the License for the
   #  specific language governing permissions and limitations under the License.
   #
   require "aws-sdk"
   
   Aws.config.update({
     region: "us-west-2",
     endpoint: "http://localhost:8000"
   })
   
   dynamodb = Aws::DynamoDB::Client.new
   
   table_name = 'Movies'
   
   year = 2015
   title = "The Big New Movie"
   
   params = {
       table_name: table_name,
       key: {
           year: year,
           title: title
       },
       update_expression: "remove info.actors[0]",
       condition_expression: "size(info.actors) > :num",
       expression_attribute_values: {
           ":num" => 3
       },
       return_values: "UPDATED_NEW"
   }
   
   begin
       result = dynamodb.update_item(params)
       puts "Updated item. ReturnValues are:"
       result.attributes["info"].each do |key, value| 
           if key == "rating" 
               puts "#{key}: #{value.to_f}" 
           else
               puts "#{key}: #{value}" 
           end
       end
   
   rescue  Aws::DynamoDB::Errors::ServiceError => error
       puts "Unable to update item:"
       puts "#{error.message}"
   end
   ```

1. To run the program, enter the following command\.

   `ruby MoviesItemOps05.rb`

   The program should fail with the following message\.

   The conditional request failed

   The program fails because the movie has three actors in it, but the condition is checking for *greater than* three actors\.

1. Modify the program so that the `ConditionExpression` looks like the following\.

   ```
   condition_expression: "size(info.actors) >= :num", 
   ```

   The condition is now *greater than or equal to 3* instead of *greater than 3*\.

1. Run the program again\. The `update_item` method should now succeed\.

## Step 3\.6: Delete an Item<a name="GettingStarted.Ruby.03.06"></a>

You can use the `delete_item` method to delete one item by specifying its primary key\. You can optionally provide a `condition_expression` to prevent the item from being deleted if the condition is not met\.

In the following example, you try to delete a specific movie item if its rating is 5 or less\.

1. Copy the following program and paste it into a file named `MoviesItemOps06.rb`\.

   ```
   #
   #  Copyright 2010-2019 Amazon.com, Inc. or its affiliates. All Rights Reserved.
   #
   #  This file is licensed under the Apache License, Version 2.0 (the "License").
   #  You may not use this file except in compliance with the License. A copy of
   #  the License is located at
   # 
   #  http://aws.amazon.com/apache2.0/
   # 
   #  This file is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR
   #  CONDITIONS OF ANY KIND, either express or implied. See the License for the
   #  specific language governing permissions and limitations under the License.
   #
   require "aws-sdk"
   
   Aws.config.update({
     region: "us-west-2",
     endpoint: "http://localhost:8000"
   })
   
   dynamodb = Aws::DynamoDB::Client.new
   
   table_name = 'Movies'
   
   year = 2015
   title = "The Big New Movie"
   
   params = {
       table_name: table_name,
       key: {
           year: year,
           title: title
       },
       condition_expression: "info.rating <= :val",
       expression_attribute_values: {
           ":val" => 5
       }
   }
   
   begin
       dynamodb.delete_item(params)
       puts "Deleted item."
   
   rescue  Aws::DynamoDB::Errors::ServiceError => error
       puts "Unable to update item:"
       puts "#{error.message}"
   end
   ```

1. To run the program, enter the following command\.

   `ruby MoviesItemOps06.rb`

   The program should fail with the following message\.

   The conditional request failed

   The program fails because the rating for this particular move is greater than 5\.

1. Modify the program to remove the condition\.

   ```
   params = {
       table_name: "Movies",
       key: {
           year: year,
           title: title
       }
   }
   ```

1. Run the program\. Now, the delete succeeds because you removed the condition\.