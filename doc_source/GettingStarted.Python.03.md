# Step 3: Create, Read, Update, and Delete an Item<a name="GettingStarted.Python.03"></a>

In this step, you perform read and write operations on an item in the `Movies` table\.

To learn more about reading and writing data, see [Working with Items in DynamoDB](WorkingWithItems.md)\.

**Topics**
+ [Step 3\.1: Create a New Item](#GettingStarted.Python.03.01)
+ [Step 3\.2: Read an Item](#GettingStarted.Python.03.02)
+ [Step 3\.3: Update an Item](#GettingStarted.Python.03.03)
+ [Step 3\.4: Increment an Atomic Counter](#GettingStarted.Python.03.04)
+ [Step 3\.5: Update an Item \(Conditionally\)](#GettingStarted.Python.03.05)
+ [Step 3\.6: Delete an Item](#GettingStarted.Python.03.06)

## Step 3\.1: Create a New Item<a name="GettingStarted.Python.03.01"></a>

In this step, you add a new item to the `Movies` table\.

1. Copy and paste the following program into a file named `MoviesItemOps01.py`.

   ```
   from __future__ import print_function # Python 2/3 compatibility
   import boto3
   import json
   import decimal
   
   # Helper class to convert a DynamoDB item to JSON.
   class DecimalEncoder(json.JSONEncoder):
       def default(self, o):
           if isinstance(o, decimal.Decimal):
               if abs(o) % 1 > 0:
                   return float(o)
               else:
                   return int(o)
           return super(DecimalEncoder, self).default(o)
   
   dynamodb = boto3.resource('dynamodb', region_name='us-west-2', endpoint_url="http://localhost:8000")
   
   table = dynamodb.Table('Movies')
   
   title = "The Big New Movie"
   year = 2015
   
   response = table.put_item(
      Item={
           'year': year,
           'title': title,
           'info': {
               'plot':"Nothing happens at all.",
               'rating': decimal.Decimal(0)
           }
       }
   )
   
   print("PutItem succeeded:")
   print(json.dumps(response, indent=4, cls=DecimalEncoder))
   ```
**Note**  
The primary key is required\. This code adds an item that has primary key \(`year`, `title`\) and `info` attributes\. The `info` attribute stores sample JSON that provides more information about the movie\.
The `DecimalEncoder` class is used to print out numbers stored using the `Decimal` class\. The Boto SDK uses the `Decimal` class to hold Amazon DynamoDB number values\.

1. To run the program, type the following command.

   `python MoviesItemOps01.py`

## Step 3\.2: Read an Item<a name="GettingStarted.Python.03.02"></a>

In the previous program, you added the following item to the table.

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

1. Copy and paste the following program into a file named `MoviesItemOps02.py`\.

   ```
   from __future__ import print_function # Python 2/3 compatibility
   import boto3
   import json
   import decimal
   from boto3.dynamodb.conditions import Key, Attr
   from botocore.exceptions import ClientError
   
   # Helper class to convert a DynamoDB item to JSON.
   class DecimalEncoder(json.JSONEncoder):
       def default(self, o):
           if isinstance(o, decimal.Decimal):
               if o % 1 > 0:
                   return float(o)
               else:
                   return int(o)
           return super(DecimalEncoder, self).default(o)
   
   dynamodb = boto3.resource("dynamodb", region_name='us-west-2', endpoint_url="http://localhost:8000")
   
   table = dynamodb.Table('Movies')
   
   title = "The Big New Movie"
   year = 2015
   
   try:
       response = table.get_item(
           Key={
               'year': year,
               'title': title
           }
       )
   except ClientError as e:
       print(e.response['Error']['Message'])
   else:
       item = response['Item']
       print("GetItem succeeded:")
       print(json.dumps(item, indent=4, cls=DecimalEncoder))
   ```

1. To run the program, type the following command.

   `python MoviesItemOps02.py`

## Step 3\.3: Update an Item<a name="GettingStarted.Python.03.03"></a>

You can use the `update_item` method to modify an existing item\. You can update values of existing attributes, add new attributes, or remove attributes\.

In this example, you perform the following updates:
+ Change the value of the existing attributes \(`rating`, `plot`\)\. 
+ Add a new list attribute \(`actors`\) to the existing `info` map\.

The item changes from how it appears in the following code.

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

The item changes to the following code. 

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

1. Copy and paste the following program into a file named `MoviesItemOps03.py`.

   ```
   from __future__ import print_function # Python 2/3 compatibility
   import boto3
   import json
   import decimal
   
   # Helper class to convert a DynamoDB item to JSON.
   class DecimalEncoder(json.JSONEncoder):
       def default(self, o):
           if isinstance(o, decimal.Decimal):
               if o % 1 > 0:
                   return float(o)
               else:
                   return int(o)
           return super(DecimalEncoder, self).default(o)
   
   dynamodb = boto3.resource('dynamodb', region_name='us-west-2', endpoint_url="http://localhost:8000")
   
   table = dynamodb.Table('Movies')
   
   title = "The Big New Movie"
   year = 2015
   
   response = table.update_item(
       Key={
           'year': year,
           'title': title
       },
       UpdateExpression="set info.rating = :r, info.plot=:p, info.actors=:a",
       ExpressionAttributeValues={
           ':r': decimal.Decimal(5.5),
           ':p': "Everything happens all at once.",
           ':a': ["Larry", "Moe", "Curly"]
       },
       ReturnValues="UPDATED_NEW"
   )
   
   print("UpdateItem succeeded:")
   print(json.dumps(response, indent=4, cls=DecimalEncoder))
   ```
**Note**  
This program uses `UpdateExpression` to describe all updates you want to perform on the specified item\.  
The `ReturnValues` parameter instructs DynamoDB to return only the updated attributes \(`UPDATED_NEW`\)\.

1. To run the program, type the following command.

   `python MoviesItemOps03.py`

## Step 3\.4: Increment an Atomic Counter<a name="GettingStarted.Python.03.04"></a>

DynamoDB supports atomic counters, which use the `update_item` method to increment or decrement the value of an existing attribute without interfering with other write requests\. \(All write requests are applied in the order in which they are received\.\)

The following program shows how to increment the `rating` for a movie\. Each time you run the program, it increments this attribute by one\. 

1. Copy and paste the following program into a file named `MoviesItemOps04.py`.

   ```
   from __future__ import print_function # Python 2/3 compatibility
   import boto3
   import json
   import decimal
   
   # Helper class to convert a DynamoDB item to JSON.
   class DecimalEncoder(json.JSONEncoder):
       def default(self, o):
           if isinstance(o, decimal.Decimal):
               if o % 1 > 0:
                   return float(o)
               else:
                   return int(o)
           return super(DecimalEncoder, self).default(o)
   
   dynamodb = boto3.resource('dynamodb', region_name='us-west-2', endpoint_url="http://localhost:8000")
   
   table = dynamodb.Table('Movies')
   
   title = "The Big New Movie"
   year = 2015
   
   response = table.update_item(
       Key={
           'year': year,
           'title': title
       },
       UpdateExpression="set info.rating = info.rating + :val",
       ExpressionAttributeValues={
           ':val': decimal.Decimal(1)
       },
       ReturnValues="UPDATED_NEW"
   )
   
   print("UpdateItem succeeded:")
   print(json.dumps(response, indent=4, cls=DecimalEncoder))
   ```

1. To run the program, type the following command.

   `python MoviesItemOps04.py`

## Step 3\.5: Update an Item \(Conditionally\)<a name="GettingStarted.Python.03.05"></a>

The following program shows how to use `UpdateItem` with a condition\. If the condition evaluates to true, the update succeeds; otherwise, the update is not performed\.

In this case, the item is updated only if there are more than three actors\.

1. Copy and paste the following program into a file named `MoviesItemOps05.py`.

   ```
   from __future__ import print_function # Python 2/3 compatibility
   import boto3
   from botocore.exceptions import ClientError
   import json
   import decimal
   
   # Helper class to convert a DynamoDB item to JSON.
   class DecimalEncoder(json.JSONEncoder):
       def default(self, o):
           if isinstance(o, decimal.Decimal):
               if o % 1 > 0:
                   return float(o)
               else:
                   return int(o)
           return super(DecimalEncoder, self).default(o)
   
   dynamodb = boto3.resource('dynamodb', region_name='us-west-2', endpoint_url="http://localhost:8000")
   
   table = dynamodb.Table('Movies')
   
   title = "The Big New Movie"
   year = 2015
   
   # Conditional update (will fail)
   print("Attempting conditional update...")
   
   try:
       response = table.update_item(
           Key={
               'year': year,
               'title': title
           },
           UpdateExpression="remove info.actors[0]",
           ConditionExpression="size(info.actors) > :num",
           ExpressionAttributeValues={
               ':num': 3
           },
           ReturnValues="UPDATED_NEW"
       )
   except ClientError as e:
       if e.response['Error']['Code'] == "ConditionalCheckFailedException":
           print(e.response['Error']['Message'])
       else:
           raise
   else:
       print("UpdateItem succeeded:")
       print(json.dumps(response, indent=4, cls=DecimalEncoder))
   ```

1. To run the program, type the following command.

   `python MoviesItemOps05.py`

   The program should fail with the following message.

   `The conditional request failed`

   The program fails because the movie has three actors in it, but the condition is checking for *greater than* three actors\.

1. Modify the program so that the `ConditionExpression` looks like the following.

   ```
   ConditionExpression="size(info.actors) >= :num", 
   ```

   The condition is now *greater than or equal to 3* instead of *greater than 3*\.

1. Run the program again\. The `UpdateItem` operation should now succeed\.

## Step 3\.6: Delete an Item<a name="GettingStarted.Python.03.06"></a>

You can use the `delete_item` method to delete one item by specifying its primary key\. Optionally, you can provide a `ConditionExpression` to prevent the item from being deleted if the condition is not met\.

In the following example, you try to delete a specific movie item if its rating is 5 or less\.

1. Copy and paste the following program into a file named `MoviesItemOps06.py`.

   ```
   from __future__ import print_function # Python 2/3 compatibility
   import boto3
   from botocore.exceptions import ClientError
   import json
   import decimal
   
   # Helper class to convert a DynamoDB item to JSON.
   class DecimalEncoder(json.JSONEncoder):
       def default(self, o):
           if isinstance(o, decimal.Decimal):
               if o % 1 > 0:
                   return float(o)
               else:
                   return int(o)
           return super(DecimalEncoder, self).default(o)
   
   dynamodb = boto3.resource('dynamodb', region_name='us-west-2', endpoint_url="http://localhost:8000")
   
   table = dynamodb.Table('Movies')
   
   title = "The Big New Movie"
   year = 2015
   
   print("Attempting a conditional delete...")
   
   try:
       response = table.delete_item(
           Key={
               'year': year,
               'title': title
           },
           ConditionExpression="info.rating <= :val",
           ExpressionAttributeValues= {
               ":val": decimal.Decimal(5)
           }
       )
   except ClientError as e:
       if e.response['Error']['Code'] == "ConditionalCheckFailedException":
           print(e.response['Error']['Message'])
       else:
           raise
   else:
       print("DeleteItem succeeded:")
       print(json.dumps(response, indent=4, cls=DecimalEncoder))
   ```

1. To run the program, type the following command.

   `python MoviesItemOps06.py`

   The program should fail with the following message.

   `The conditional request failed`

   The program fails because the rating for this particular move is greater than 5\.

1. Modify the program to remove the condition in `table.delete_item`.

   ```
   response = table.delete_item(
       Key={
           'year': year,
           'title': title
       }
   )
   ```

1. Run the program\. Now, the delete succeeds because you removed the condition\.
