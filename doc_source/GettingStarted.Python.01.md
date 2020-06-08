# Step 1: Create a Table with Python<a name="GettingStarted.Python.01"></a>

In this step, you create a table named `Movies`\. The primary key for the table is composed of the following attributes:
+ `year` – The partition key\. The `AttributeType` is `N` for number\.
+ `title` – The sort key\. The `AttributeType` is `S` for string\.

1. Copy the following program and paste it into a file named `MoviesCreateTable.py`\.

   ```
   import boto3
   
   
   def create_movie_table(dynamodb=None):
       if not dynamodb:
           dynamodb = boto3.resource('dynamodb', endpoint_url="http://localhost:8000")
   
       table = dynamodb.create_table(
           TableName='Movies',
           KeySchema=[
               {
                   'AttributeName': 'year',
                   'KeyType': 'HASH'  # Partition key
               },
               {
                   'AttributeName': 'title',
                   'KeyType': 'RANGE'  # Sort key
               }
           ],
           AttributeDefinitions=[
               {
                   'AttributeName': 'year',
                   'AttributeType': 'N'
               },
               {
                   'AttributeName': 'title',
                   'AttributeType': 'S'
               },
   
           ],
           ProvisionedThroughput={
               'ReadCapacityUnits': 10,
               'WriteCapacityUnits': 10
           }
       )
       return table
   
   
   if __name__ == '__main__':
       movie_table = create_movie_table()
       print("Table status:", movie_table.table_status)
   ```
**Note**  
You set the endpoint to indicate that you are creating the table in the [downloadable version of DynamoDB](DynamoDBLocal) on your computer\.
In the `create_table` call, you specify the table name, primary key attributes, and its data types\.
The `ProvisionedThroughput` parameter is required\. However, the downloadable version of DynamoDB ignores it\. \(Provisioned throughput is beyond the scope of this exercise\.\)
These examples use the Python 3 style `print` function\. The line `from __future__ import print_function` enables Python 3 printing in Python 2\.6 and later\.

1. To run the program, enter the following command\.

   `python MoviesCreateTable.py`

To learn more about managing tables, see [Working with Tables and Data in DynamoDB](WorkingWithTables.md)\.