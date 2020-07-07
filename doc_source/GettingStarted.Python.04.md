# Step 4: Query and Scan the Data<a name="GettingStarted.Python.04"></a>

You can use the `query` method to retrieve data from a table\. You must specify a partition key value\. The sort key is optional\.

The primary key for the `Movies` table is composed of the following:
+ `year` – The partition key\. The attribute type is `number`\. 
+ `title` – The sort key\. The attribute type is `string`\.

To find all movies released during a year, you need to specify only the `year`\. You can also provide the `title` to retrieve a subset of movies based on some condition \(on the sort key\)\. For example, you can find movies released in 2014 that have a title starting with the letter "A"\.

In addition to the `query` method, you can use the `scan` method to retrieve all the table data\.

To learn more about querying and scanning data, see [Working with Queries in DynamoDB](Query.md) and [Working with Scans in DynamoDB](Scan.md), respectively\. 

**Topics**
+ [Step 4\.1: Query \- All Movies Released in a Year](#GettingStarted.Python.04.Query.01)
+ [Step 4\.2: Query \- All Movies Released in a Year with Certain Titles](#GettingStarted.Python.04.Query.02)
+ [Step 4\.3: Scan](#GettingStarted.Python.04.Scan)

## Step 4\.1: Query \- All Movies Released in a Year<a name="GettingStarted.Python.04.Query.01"></a>

The program included in this step retrieves all movies released in the `year` 1985\.

1. Copy the following program and paste it into a file named `MoviesQuery01.py`\.

   ```
   import boto3
   from boto3.dynamodb.conditions import Key
   
   
   def query_movies(year, dynamodb=None):
       if not dynamodb:
           dynamodb = boto3.resource('dynamodb', endpoint_url="http://localhost:8000")
   
       table = dynamodb.Table('Movies')
       response = table.query(
           KeyConditionExpression=Key('year').eq(year)
       )
       return response['Items']
   
   
   if __name__ == '__main__':
       query_year = 1985
       print(f"Movies from {query_year}")
       movies = query_movies(query_year)
       for movie in movies:
           print(movie['year'], ":", movie['title'])
   ```
**Note**  
The Boto 3 SDK constructs a `ConditionExpression` for you when you use the `Key` and `Attr` functions imported from `boto3.dynamodb.conditions`\. You can also specify a `ConditionExpression` as a string\.  
For a list of available conditions for Amazon DynamoDB, see [DynamoDB Conditions](http://boto3.amazonaws.com/v1/documentation/api/latest/reference/customizations/dynamodb.html#dynamodb-conditions) in *AWS SDK for Python \(Boto3\) Getting Started*\.  
For more information, see [Condition Expressions](Expressions.ConditionExpressions.md)\.

1. To run the program, enter the following command\.

   `python MoviesQuery01.py`

**Note**  
The preceding program shows how to query a table by its primary key attributes\. In DynamoDB, you can optionally create one or more secondary indexes on a table and query those indexes in the same way that you query a table\. Secondary indexes give your applications additional flexibility by allowing queries on non\-key attributes\. For more information, see [Improving Data Access with Secondary Indexes](SecondaryIndexes.md)\.

## Step 4\.2: Query \- All Movies Released in a Year with Certain Titles<a name="GettingStarted.Python.04.Query.02"></a>

The program included in this step retrieves all movies released in `year` 1992 with `title` beginning with the letter "A" through the letter "L"\.

1. Copy the following program and paste it into a file named `MoviesQuery02.py`\.

   ```
   from pprint import pprint
   import boto3
   from boto3.dynamodb.conditions import Key
   
   
   def query_and_project_movies(year, title_range, dynamodb=None):
       if not dynamodb:
           dynamodb = boto3.resource('dynamodb', endpoint_url="http://localhost:8000")
   
       table = dynamodb.Table('Movies')
       print(f"Get year, title, genres, and lead actor")
   
       # Expression attribute names can only reference items in the projection expression.
       response = table.query(
           ProjectionExpression="#yr, title, info.genres, info.actors[0]",
           ExpressionAttributeNames={"#yr": "year"},
           KeyConditionExpression=
               Key('year').eq(year) & Key('title').between(title_range[0], title_range[1])
       )
       return response['Items']
   
   
   if __name__ == '__main__':
       query_year = 1992
       query_range = ('A', 'L')
       print(f"Get movies from {query_year} with titles from "
             f"{query_range[0]} to {query_range[1]}")
       movies = query_and_project_movies(query_year, query_range)
       for movie in movies:
           print(f"\n{movie['year']} : {movie['title']}")
           pprint(movie['info'])
   ```

1. To run the program, enter the following command\.

   `python MoviesQuery02.py`

## Step 4\.3: Scan<a name="GettingStarted.Python.04.Scan"></a>

The `scan` method reads every item in the entire table and returns all the data in the table\. You can provide an optional `filter_expression` so that only the items matching your criteria are returned\. However, the filter is applied only after the entire table has been scanned\.

The following program scans the entire `Movies` table, which contains approximately 5,000 items\. The scan specifies the optional filter to retrieve only the movies from the 1950s \(approximately 100 items\) and discard all the others\.

1. Copy the following program and paste it into a file named `MoviesScan.py`\.

   ```
   from pprint import pprint
   import boto3
   from boto3.dynamodb.conditions import Key
   
   
   def scan_movies(year_range, display_movies, dynamodb=None):
       if not dynamodb:
           dynamodb = boto3.resource('dynamodb', endpoint_url="http://localhost:8000")
   
       table = dynamodb.Table('Movies')
       scan_kwargs = {
           'FilterExpression': Key('year').between(*year_range),
           'ProjectionExpression': "#yr, title, info.rating",
           'ExpressionAttributeNames': {"#yr": "year"}
       }
   
       done = False
       start_key = None
       while not done:
           if start_key:
               scan_kwargs['ExclusiveStartKey'] = start_key
           response = table.scan(**scan_kwargs)
           display_movies(response.get('Items', []))
           start_key = response.get('LastEvaluatedKey', None)
           done = start_key is None
   
   
   if __name__ == '__main__':
       def print_movies(movies):
           for movie in movies:
               print(f"\n{movie['year']} : {movie['title']}")
               pprint(movie['info'])
   
       query_range = (1950, 1959)
       print(f"Scanning for movies released from {query_range[0]} to {query_range[1]}...")
       scan_movies(query_range, print_movies)
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