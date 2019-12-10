# Step 4: Query and Scan the Data<a name="GettingStarted.Python.04"></a>

You can use the `query` method to retrieve data from a table\. You must specify a partition key value\. The sort key is optional\.

The primary key for the `Movies` table is composed of the following:
+ `year` – The partition key\. The attribute type is `number`\. 
+ `title` – The sort key\. The attribute type is `string`\.

To find all movies released during a year, you need to specify only the `year`\. You can also provide the `title` to retrieve a subset of movies based on some condition \(on the sort key\)\. For example, you can find movies released in 2014 that have a title starting with the letter "A"\.

In addition to the `query` method, you can use the `scan` method to retrieve all the table data\.

To learn more about querying and scanning data, see [Working with Queries](Query.md) and [Working with Scans](Scan.md), respectively\. 

**Topics**
+ [Step 4\.1: Query \- All Movies Released in a Year](#GettingStarted.Python.04.Query.01)
+ [Step 4\.2: Query \- All Movies Released in a Year with Certain Titles](#GettingStarted.Python.04.Query.02)
+ [Step 4\.3: Scan](#GettingStarted.Python.04.Scan)

## Step 4\.1: Query \- All Movies Released in a Year<a name="GettingStarted.Python.04.Query.01"></a>

The program included in this step retrieves all movies released in the `year` 1985\.

1. Copy the following program and paste it into a file named `MoviesQuery01.py`\.

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
   from __future__ import print_function # Python 2/3 compatibility
   import boto3
   import json
   import decimal
   from boto3.dynamodb.conditions import Key, Attr
   
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
   
   print("Movies from 1985")
   
   response = table.query(
       KeyConditionExpression=Key('year').eq(1985)
   )
   
   for i in response['Items']:
       print(i['year'], ":", i['title'])
   ```
**Note**  
The Boto 3 SDK constructs a `ConditionExpression` for you when you use the `Key` and `Attr` functions imported from `boto3.dynamodb.conditions`\. You can also specify a `ConditionExpression` as a string\.  
For a list of available conditions for Amazon DynamoDB, see [DynamoDB Conditions](http://boto3.amazonaws.com/v1/documentation/api/latest/reference/customizations/dynamodb.html#dynamodb-conditions) in *AWS SDK for Python \(Boto 3\) Getting Started*\.  
For more information, see [Condition Expressions](Expressions.ConditionExpressions.md)\.

1. To run the program, enter the following command\.

   `python MoviesQuery01.py`

**Note**  
The preceding program shows how to query a table by its primary key attributes\. In DynamoDB, you can optionally create one or more secondary indexes on a table and query those indexes in the same way that you query a table\. Secondary indexes give your applications additional flexibility by allowing queries on non\-key attributes\. For more information, see [Improving Data Access with Secondary Indexes](SecondaryIndexes.md)\. 

## Step 4\.2: Query \- All Movies Released in a Year with Certain Titles<a name="GettingStarted.Python.04.Query.02"></a>

The program included in this step retrieves all movies released in `year` 1992 with `title` beginning with the letter "A" through the letter "L"\.

1. Copy the following program and paste it into a file named `MoviesQuery02.py`\.

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
   from __future__ import print_function # Python 2/3 compatibility
   import boto3
   import json
   import decimal
   from boto3.dynamodb.conditions import Key, Attr
   
   # Helper class to convert a DynamoDB item to JSON.
   class DecimalEncoder(json.JSONEncoder):
       def default(self, o):
           if isinstance(o, decimal.Decimal):
               return str(o)
           return super(DecimalEncoder, self).default(o)
   
   dynamodb = boto3.resource('dynamodb', region_name='us-west-2', endpoint_url="http://localhost:8000")
   
   table = dynamodb.Table('Movies')
   
   print("Movies from 1992 - titles A-L, with genres and lead actor")
   
   response = table.query(
       ProjectionExpression="#yr, title, info.genres, info.actors[0]",
       ExpressionAttributeNames={ "#yr": "year" }, # Expression Attribute Names for Projection Expression only.
       KeyConditionExpression=Key('year').eq(1992) & Key('title').between('A', 'L')
   )
   
   for i in response[u'Items']:
       print(json.dumps(i, cls=DecimalEncoder))
   ```

1. To run the program, enter the following command\.

   `python MoviesQuery02.py`

## Step 4\.3: Scan<a name="GettingStarted.Python.04.Scan"></a>

The `scan` method reads every item in the entire table and returns all the data in the table\. You can provide an optional `filter_expression` so that only the items matching your criteria are returned\. However, the filter is applied only after the entire table has been scanned\.

The following program scans the entire `Movies` table, which contains approximately 5,000 items\. The scan specifies the optional filter to retrieve only the movies from the 1950s \(approximately 100 items\) and discard all the others\.

1. Copy the following program and paste it into a file named `MoviesScan.py`\.

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
   from __future__ import print_function # Python 2/3 compatibility
   import boto3
   import json
   import decimal
   from boto3.dynamodb.conditions import Key, Attr
   
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
   
   fe = Key('year').between(1950, 1959)
   pe = "#yr, title, info.rating"
   # Expression Attribute Names for Projection Expression only.
   ean = { "#yr": "year", }
   esk = None
   
   
   response = table.scan(
       FilterExpression=fe,
       ProjectionExpression=pe,
       ExpressionAttributeNames=ean
       )
   
   for i in response['Items']:
       print(json.dumps(i, cls=DecimalEncoder))
   
   while 'LastEvaluatedKey' in response:
       response = table.scan(
           ProjectionExpression=pe,
           FilterExpression=fe,
           ExpressionAttributeNames= ean,
           ExclusiveStartKey=response['LastEvaluatedKey']
           )
   
       for i in response['Items']:
           print(json.dumps(i, cls=DecimalEncoder))
   ```

   In the code, note the following:
   + `ProjectionExpression` specifies the attributes you want in the scan result\.
   + `FilterExpression` specifies a condition that returns only items that satisfy the condition\. All other items are discarded\.
   + The `scan` method returns a subset of the items each time, called a *page*\. The `LastEvaluatedKey` value in the response is then passed to the `scan` method via the `ExclusiveStartKey` parameter\. When the last page is returned, `LastEvaluatedKey` is not part of the response\.
**Note**  
`ExpressionAttributeNames` provides name substitution\. We use this because `year` is a reserved word in DynamoDB—you can't use it directly in any expression, including `KeyConditionExpression`\. You can use the expression attribute name `#yr` to address this\.
`ExpressionAttributeValues` provides value substitution\. You use this because you can't use literals in any expression, including `KeyConditionExpression`\. You can use the expression attribute value `:yyyy` to address this\.

1. To run the program, enter the following command\.

   `python MoviesScan.py`

**Note**  
You can also use the `Scan` operation with any secondary indexes that you create on the table\. For more information, see [Improving Data Access with Secondary Indexes](SecondaryIndexes.md)\.