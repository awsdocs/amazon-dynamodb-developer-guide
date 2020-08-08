# Step 3: Create, Read, Update, and Delete an Item with Python<a name="GettingStarted.Python.03"></a>

In this step, you perform read and write operations on an item in the `Movies` table\.

To learn more about reading and writing data, see [Working with Items and Attributes](WorkingWithItems.md)\.

**Topics**
+ [Step 3\.1: Create a New Item](#GettingStarted.Python.03.01)
+ [Step 3\.2: Read an Item](#GettingStarted.Python.03.02)
+ [Step 3\.3: Update an Item](#GettingStarted.Python.03.03)
+ [Step 3\.4: Increment an Atomic Counter](#GettingStarted.Python.03.04)
+ [Step 3\.5: Update an Item \(Conditionally\)](#GettingStarted.Python.03.05)
+ [Step 3\.6: Delete an Item](#GettingStarted.Python.03.06)

## Step 3\.1: Create a New Item<a name="GettingStarted.Python.03.01"></a>

In this step, you add a new item to the `Movies` table\.

1. Copy the following program and paste it into a file named `MoviesItemOps01.py`\.

   ```
   from pprint import pprint
   import boto3
   
   
   def put_movie(title, year, plot, rating, dynamodb=None):
       if not dynamodb:
           dynamodb = boto3.resource('dynamodb', endpoint_url="http://localhost:8000")
   
       table = dynamodb.Table('Movies')
       response = table.put_item(
          Item={
               'year': year,
               'title': title,
               'info': {
                   'plot': plot,
                   'rating': rating
               }
           }
       )
       return response
   
   
   if __name__ == '__main__':
       movie_resp = put_movie("The Big New Movie", 2015,
                              "Nothing happens at all.", 0)
       print("Put movie succeeded:")
       pprint(movie_resp, sort_dicts=False)
   ```
**Note**  
The primary key is required\. This code adds an item that has primary key \(`year`, `title`\) and `info` attributes\. The `info` attribute stores sample JSON that provides more information about the movie\.
The `DecimalEncoder` class is used to print out numbers stored using the `Decimal` class\. The Boto SDK uses the `Decimal` class to hold Amazon DynamoDB number values\.

1. To run the program, enter the following command\.

   `python MoviesItemOps01.py`

## Step 3\.2: Read an Item<a name="GettingStarted.Python.03.02"></a>

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

1. Copy the following program and paste it into a file named `MoviesItemOps02.py`\.

   ```
   from pprint import pprint
   import boto3
   from botocore.exceptions import ClientError
   
   
   def get_movie(title, year, dynamodb=None):
       if not dynamodb:
           dynamodb = boto3.resource('dynamodb', endpoint_url="http://localhost:8000")
   
       table = dynamodb.Table('Movies')
   
       try:
           response = table.get_item(Key={'year': year, 'title': title})
       except ClientError as e:
           print(e.response['Error']['Message'])
       else:
           return response['Item']
   
   
   if __name__ == '__main__':
       movie = get_movie("The Big New Movie", 2015,)
       if movie:
           print("Get movie succeeded:")
           pprint(movie, sort_dicts=False)
   ```

1. To run the program, enter the following command\.

   `python MoviesItemOps02.py`

## Step 3\.3: Update an Item<a name="GettingStarted.Python.03.03"></a>

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

1. Copy the following program and paste it into a file named `MoviesItemOps03.py`\.

   ```
   from decimal import Decimal
   from pprint import pprint
   import boto3
   
   
   def update_movie(title, year, rating, plot, actors, dynamodb=None):
       if not dynamodb:
           dynamodb = boto3.resource('dynamodb', endpoint_url="http://localhost:8000")
   
       table = dynamodb.Table('Movies')
   
       response = table.update_item(
           Key={
               'year': year,
               'title': title
           },
           UpdateExpression="set info.rating=:r, info.plot=:p, info.actors=:a",
           ExpressionAttributeValues={
               ':r': Decimal(rating),
               ':p': plot,
               ':a': actors
           },
           ReturnValues="UPDATED_NEW"
       )
       return response
   
   
   if __name__ == '__main__':
       update_response = update_movie(
           "The Big New Movie", 2015, 5.5, "Everything happens all at once.",
           ["Larry", "Moe", "Curly"])
       print("Update movie succeeded:")
       pprint(update_response, sort_dicts=False)
   ```
**Note**  
This program uses `UpdateExpression` to describe all updates you want to perform on the specified item\.  
The `ReturnValues` parameter instructs DynamoDB to return only the updated attributes \(`UPDATED_NEW`\)\.

1. To run the program, enter the following command\.

   `python MoviesItemOps03.py`

## Step 3\.4: Increment an Atomic Counter<a name="GettingStarted.Python.03.04"></a>

DynamoDB supports atomic counters, which use the `update_item` method to increment or decrement the value of an existing attribute without interfering with other write requests\. \(All write requests are applied in the order in which they are received\.\)

The following program shows how to increment the `rating` for a movie\. Each time you run the program, it increments this attribute by one\. 

1. Copy the following program and paste it into a file named `MoviesItemOps04.py`\.

   ```
   from decimal import Decimal
   from pprint import pprint
   import boto3
   
   
   def increase_rating(title, year, rating_increase, dynamodb=None):
       if not dynamodb:
           dynamodb = boto3.resource('dynamodb', endpoint_url="http://localhost:8000")
   
       table = dynamodb.Table('Movies')
   
       response = table.update_item(
           Key={
               'year': year,
               'title': title
           },
           UpdateExpression="set info.rating = info.rating + :val",
           ExpressionAttributeValues={
               ':val': Decimal(rating_increase)
           },
           ReturnValues="UPDATED_NEW"
       )
       return response
   
   
   if __name__ == '__main__':
       update_response = increase_rating("The Big New Movie", 2015, 1)
       print("Update movie succeeded:")
       pprint(update_response, sort_dicts=False)
   ```

1. To run the program, enter the following command\.

   `python MoviesItemOps04.py`

## Step 3\.5: Update an Item \(Conditionally\)<a name="GettingStarted.Python.03.05"></a>

The following program shows how to use `UpdateItem` with a condition\. If the condition evaluates to true, the update succeeds; otherwise, the update is not performed\.

In this case, the item is updated only if there are more than three actors\.

1. Copy the following program and paste it into a file named `MoviesItemOps05.py`\.

   ```
   from pprint import pprint
   import boto3
   from botocore.exceptions import ClientError
   
   
   def remove_actors(title, year, actor_count, dynamodb=None):
       if not dynamodb:
           dynamodb = boto3.resource('dynamodb', endpoint_url="http://localhost:8000")
   
       table = dynamodb.Table('Movies')
   
       try:
           response = table.update_item(
               Key={
                   'year': year,
                   'title': title
               },
               UpdateExpression="remove info.actors[0]",
               ConditionExpression="size(info.actors) > :num",
               ExpressionAttributeValues={':num': actor_count},
               ReturnValues="UPDATED_NEW"
           )
       except ClientError as e:
           if e.response['Error']['Code'] == "ConditionalCheckFailedException":
               print(e.response['Error']['Message'])
           else:
               raise
       else:
           return response
   
   
   if __name__ == '__main__':
       print("Attempting conditional update (expecting failure)...")
       update_response = remove_actors("The Big New Movie", 2015, 3)
       if update_response:
           print("Update movie succeeded:")
           pprint(update_response, sort_dicts=False)
   ```

1. To run the program, enter the following command\.

   `python MoviesItemOps05.py`

   The program should fail with the following message\.

   The conditional request failed

   The program fails because the movie has three actors in it, but the condition is checking for *greater than* three actors\.

1. Modify the program so that the `ConditionExpression` looks like the following\.

   ```
   ConditionExpression="size(info.actors) >= :num", 
   ```

   The condition is now *greater than or equal to 3* instead of *greater than 3*\.

1. Run the program again\. The `UpdateItem` operation should now succeed\.

## Step 3\.6: Delete an Item<a name="GettingStarted.Python.03.06"></a>

You can use the `delete_item` method to delete one item by specifying its primary key\. Optionally, you can provide a `ConditionExpression` to prevent the item from being deleted if the condition is not met\.

In the following example, you try to delete a specific movie item if its rating is 5 or less\.

1. Copy the following program and paste it into a file named `MoviesItemOps06.py`\.

   ```
   from decimal import Decimal
   from pprint import pprint
   import boto3
   from botocore.exceptions import ClientError
   
   
   def delete_underrated_movie(title, year, rating, dynamodb=None):
       if not dynamodb:
           dynamodb = boto3.resource('dynamodb', endpoint_url="http://localhost:8000")
   
       table = dynamodb.Table('Movies')
   
       try:
           response = table.delete_item(
               Key={
                   'year': year,
                   'title': title
               },
               ConditionExpression="info.rating <= :val",
               ExpressionAttributeValues={
                   ":val": Decimal(rating)
               }
           )
       except ClientError as e:
           if e.response['Error']['Code'] == "ConditionalCheckFailedException":
               print(e.response['Error']['Message'])
           else:
               raise
       else:
           return response
   
   
   if __name__ == '__main__':
       print("Attempting a conditional delete...")
       delete_response = delete_underrated_movie("The Big New Movie", 2015, 5)
       if delete_response:
           print("Delete movie succeeded:")
           pprint(delete_response, sort_dicts=False)
   ```

1. To run the program, enter the following command\.

   `python MoviesItemOps06.py`

   The program should fail with the following message\.

   The conditional request failed

   The program fails because the rating for this particular movie is greater than 5\.

1. Modify the program to remove the condition in `table.delete_item`\.

   ```
   response = table.delete_item(
       Key={
           'year': year,
           'title': title
       }
   )
   ```

1. Run the program\. Now, the delete succeeds because you removed the condition\.