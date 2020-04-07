# Step 3: Create, Read, Update, and Delete an Item<a name="GettingStarted.PHP.03"></a>

In this step, you perform read and write operations on an item in the `Movies` table\.

To learn more about reading and writing data, see [Working with Items and Attributes](WorkingWithItems.md)\.

**Topics**
+ [Step 3\.1: Create a New Item](#GettingStarted.PHP.03.01)
+ [Step 3\.2: Read an Item](#GettingStarted.PHP.03.02)
+ [Step 3\.3: Update an Item](#GettingStarted.PHP.03.03)
+ [Step 3\.4: Increment an Atomic Counter](#GettingStarted.PHP.03.04)
+ [Step 3\.5: Update an Item \(Conditionally\)](#GettingStarted.PHP.03.05)
+ [Step 3\.6: Delete an Item](#GettingStarted.PHP.03.06)

## Step 3\.1: Create a New Item<a name="GettingStarted.PHP.03.01"></a>

In this step, you add a new item to the `Movies` table\.

1. Copy the following program and paste it into a file named `MoviesItemOps01.php`\.

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
   
   require 'vendor/autoload.php';
   
   date_default_timezone_set('UTC');
   
   use Aws\DynamoDb\Exception\DynamoDbException;
   use Aws\DynamoDb\Marshaler;
   
   $sdk = new Aws\Sdk([
       'endpoint'   => 'http://localhost:8000',
       'region'   => 'us-west-2',
       'version'  => 'latest'
   ]);
   
   $dynamodb = $sdk->createDynamoDb();
   $marshaler = new Marshaler();
   
   $tableName = 'Movies';
   
   $year = 2015;
   $title = 'The Big New Movie';
   
   $item = $marshaler->marshalJson('
       {
           "year": ' . $year . ',
           "title": "' . $title . '",
           "info": {
               "plot": "Nothing happens at all.",
               "rating": 0
           }
       }
   ');
   
   $params = [
       'TableName' => 'Movies',
       'Item' => $item
   ];
   
   
   try {
       $result = $dynamodb->putItem($params);
       echo "Added item: $year - $title\n";
   
   } catch (DynamoDbException $e) {
       echo "Unable to add item:\n";
       echo $e->getMessage() . "\n";
   }
   ```
**Note**  
The primary key is required\. This code adds an item that has primary key \(`year`, `title`\) and `info` attributes\. The `info` attribute stores a map that provides more information about the movie\.

1. To run the program, enter the following command\.

   `php MoviesItemOps01.php`

## Step 3\.2: Read an Item<a name="GettingStarted.PHP.03.02"></a>

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

1. Copy the following program and paste it into a file named `MoviesItemOps02.php`\.

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
   
   require 'vendor/autoload.php';
   
   date_default_timezone_set('UTC');
   
   use Aws\DynamoDb\Exception\DynamoDbException;
   use Aws\DynamoDb\Marshaler;
   
   $sdk = new Aws\Sdk([
       'endpoint'   => 'http://localhost:8000',
       'region'   => 'us-west-2',
       'version'  => 'latest'
   ]);
   
   $dynamodb = $sdk->createDynamoDb();
   $marshaler = new Marshaler();
   
   $tableName = 'Movies';
   
   $year = 2015;
   $title = 'The Big New Movie';
   
   $key = $marshaler->marshalJson('
       {
           "year": ' . $year . ', 
           "title": "' . $title . '"
       }
   ');
   
   $params = [
       'TableName' => $tableName,
       'Key' => $key
   ];
   
   try {
       $result = $dynamodb->getItem($params);
       print_r($result["Item"]);
   
   } catch (DynamoDbException $e) {
       echo "Unable to get item:\n";
       echo $e->getMessage() . "\n";
   }
   ```

1. To run the program, enter the following command\.

   `php MoviesItemOps02.php`

## Step 3\.3: Update an Item<a name="GettingStarted.PHP.03.03"></a>

You can use the `updateItem` method to modify an existing item\. You can update values of existing attributes, add new attributes, or remove attributes\.

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

1. Copy the following program and paste it into a file named `MoviesItemOps03.php`\.

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
   
   require 'vendor/autoload.php';
   
   date_default_timezone_set('UTC');
   
   use Aws\DynamoDb\Exception\DynamoDbException;
   use Aws\DynamoDb\Marshaler;
   
   $sdk = new Aws\Sdk([
       'endpoint'   => 'http://localhost:8000',
       'region'   => 'us-west-2',
       'version'  => 'latest'
   ]);
   
   $dynamodb = $sdk->createDynamoDb();
   $marshaler = new Marshaler();
   
   $tableName = 'Movies';
   
   $year = 2015;
   $title = 'The Big New Movie';
   
   $key = $marshaler->marshalJson('
       {
           "year": ' . $year . ', 
           "title": "' . $title . '"
       }
   ');
   
    
   $eav = $marshaler->marshalJson('
       {
           ":r": 5.5 ,
           ":p": "Everything happens all at once.",
           ":a": [ "Larry", "Moe", "Curly" ]
       }
   ');
   
   $params = [
       'TableName' => $tableName,
       'Key' => $key,
       'UpdateExpression' => 
           'set info.rating = :r, info.plot=:p, info.actors=:a',
       'ExpressionAttributeValues'=> $eav,
       'ReturnValues' => 'UPDATED_NEW'
   ];
   
   try {
       $result = $dynamodb->updateItem($params);
       echo "Updated item.\n";
       print_r($result['Attributes']);
   
   } catch (DynamoDbException $e) {
       echo "Unable to update item:\n";
       echo $e->getMessage() . "\n";
   }
   ```
**Note**  
This program uses `UpdateExpression` to describe all updates you want to perform on the specified item\.  
The `ReturnValues` parameter instructs Amazon DynamoDB to return only the updated attributes \(`UPDATED_NEW`\)\.

1. To run the program, enter the following command\.

   `php MoviesItemOps03.php`

## Step 3\.4: Increment an Atomic Counter<a name="GettingStarted.PHP.03.04"></a>

DynamoDB supports atomic counters, which use the `updateItem` method to increment or decrement the value of an existing attribute without interfering with other write requests\. \(All write requests are applied in the order in which they are received\.\)

The following program shows how to increment the `rating` for a movie\. Each time you run the program, it increments this attribute by one\. 

1. Copy the following program and paste it into a file named `MoviesItemOps04.php`\.

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
   
   require 'vendor/autoload.php';
   
   date_default_timezone_set('UTC');
   
   use Aws\DynamoDb\Exception\DynamoDbException;
   use Aws\DynamoDb\Marshaler;
   
   $sdk = new Aws\Sdk([
       'endpoint'   => 'http://localhost:8000',
       'region'   => 'us-west-2',
       'version'  => 'latest'
   ]);
   
   $dynamodb = $sdk->createDynamoDb();
   $marshaler = new Marshaler();
   
   $tableName = 'Movies';
   
   $year = 2015;
   $title = 'The Big New Movie';
   
   $key = $marshaler->marshalJson('
       {
           "year": ' . $year . ', 
           "title": "' . $title . '"
       }
   ');
   
   $eav = $marshaler->marshalJson('
       {
           ":val": 1
       }
   ');
   
   $params = [
       'TableName' => $tableName,
       'Key' => $key,
       'UpdateExpression' => 'set info.rating = info.rating + :val',
       'ExpressionAttributeValues'=> $eav,
       'ReturnValues' => 'UPDATED_NEW'
   ];
   
   try {
       $result = $dynamodb->updateItem($params);
       echo "Updated item. ReturnValues are:\n";
       print_r($result['Attributes']);
   
   } catch (DynamoDbException $e) {
       echo "Unable to update item:\n";
       echo $e->getMessage() . "\n";
   }
   ```

1. To run the program, enter the following command\.

   `php MoviesItemOps04.php`

## Step 3\.5: Update an Item \(Conditionally\)<a name="GettingStarted.PHP.03.05"></a>

The following program shows how to use `UpdateItem` with a condition\. If the condition evaluates to true, the update succeeds; otherwise, the update is not performed\.

In this case, the item is updated only if there are more than three actors in the movie\.

1. Copy the following program and paste it into a file named `MoviesItemOps05.php`\.

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
   
   require 'vendor/autoload.php';
   
   date_default_timezone_set('UTC');
   
   use Aws\DynamoDb\Exception\DynamoDbException;
   use Aws\DynamoDb\Marshaler;
   
   $sdk = new Aws\Sdk([
       'endpoint'   => 'http://localhost:8000',
       'region'   => 'us-west-2',
       'version'  => 'latest'
   ]);
   
   $dynamodb = $sdk->createDynamoDb();
   $marshaler = new Marshaler();
   
   $tableName = 'Movies';
   
   $year = 2015;
   $title = 'The Big New Movie';
   
   $key = $marshaler->marshalJson('
       {
           "year": ' . $year . ', 
           "title": "' . $title . '"
       }
   ');
   
   $eav = $marshaler->marshalJson('
       {
           ":num": 3
       }
   ');
   
   $params = [
       'TableName' => $tableName,
       'Key' => $key,
       'UpdateExpression' => 'remove info.actors[0]',
       'ConditionExpression' => 'size(info.actors) > :num',
       'ExpressionAttributeValues'=> $eav,
       'ReturnValues' => 'UPDATED_NEW'
   ];
   
   try {
       $result = $dynamodb->updateItem($params);
       echo "Updated item. ReturnValues are:\n";
       print_r($result['Attributes']);
   
   } catch (DynamoDbException $e) {
       echo "Unable to update item:\n";
       echo $e->getMessage() . "\n";
   }
   ```

1. To run the program, enter the following command\.

   `php MoviesItemOps05.php`

   The program should fail with the following message\.

   The conditional request failed

   The program fails because the movie has three actors in it, but the condition is checking for *greater than* three actors\.

1. Modify the program so that the `ConditionExpression` looks like the following\.

   ```
   ConditionExpression="size(info.actors) >= :num",
   ```

   The condition is now *greater than or equal to 3* instead of *greater than 3*\.

1. Run the program again\. The `UpdateItem` operation should now succeed\.

## Step 3\.6: Delete an Item<a name="GettingStarted.PHP.03.06"></a>

You can use the `deleteItem` method to delete one item by specifying its primary key\. You can optionally provide a `ConditionExpression` to prevent the item from being deleted if the condition is not met\.

In the following example, you try to delete a specific movie item if its rating is 5 or less\.

1. Copy the following program and paste it into a file named `MoviesItemOps06.php`\.

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
   
   require 'vendor/autoload.php';
   
   date_default_timezone_set('UTC');
   
   use Aws\DynamoDb\Exception\DynamoDbException;
   use Aws\DynamoDb\Marshaler;
   
   $sdk = new Aws\Sdk([
       'endpoint'   => 'http://localhost:8000',
       'region'   => 'us-west-2',
       'version'  => 'latest'
   ]);
   
   $dynamodb = $sdk->createDynamoDb();
   $marshaler = new Marshaler();
   
   $tableName = 'Movies';
   
   $year = 2015;
   $title = 'The Big New Movie';
   
   $key = $marshaler->marshalJson('
       {
           "year": ' . $year . ', 
           "title": "' . $title . '"
       }
   ');
   
   $eav = $marshaler->marshalJson('
       {
           ":val": 5 
       }
   ');
   
   $params = [
       'TableName' => $tableName,
       'Key' => $key, 
       'ConditionExpression' => 'info.rating <= :val',
       'ExpressionAttributeValues'=> $eav
   ];
   
   try {
       $result = $dynamodb->deleteItem($params);
       echo "Deleted item.\n";
   
   } catch (DynamoDbException $e) {
       echo "Unable to delete item:\n";
       echo $e->getMessage() . "\n";
   }
   ```

1. To run the program, enter the following command\.

   `php MoviesItemOps06.php`

   The program should fail with the following message\.

   The conditional request failed

   The program fails because the rating for this particular move is greater than 5\.

1. Modify the program to remove the condition\.

   ```
   $params = [
       'TableName' => $tableName,
       'Key' => $key
   ];
   ```

1. Run the program\. Now, the delete succeeds because you removed the condition\.