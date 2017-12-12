# Step 5: \(Optional\) Delete the Table<a name="GettingStarted.Python.05"></a>

 To delete the `Movies` table: 

1. Copy and paste the following program into a file named `MoviesDeleteTable.py`:

   ```
   from __future__ import print_function # Python 2/3 compatibility
   import boto3
   
   dynamodb = boto3.resource('dynamodb', region_name='us-west-2', endpoint_url="http://localhost:8000")
   
   table = dynamodb.Table('Movies')
   
   table.delete()
   ```

1. To run the program, type the following command:

   `python MoviesDeleteTable.py`