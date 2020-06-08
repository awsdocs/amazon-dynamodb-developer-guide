# Step 5: \(Optional\) Delete the Table<a name="GettingStarted.Python.05"></a>

 To delete the `Movies` table: 

1. Copy the following program and paste it into a file named `MoviesDeleteTable.py`\.

   ```
   import boto3
   
   def delete_movie_table(dynamodb=None):
       if not dynamodb:
           dynamodb = boto3.resource('dynamodb', endpoint_url="http://localhost:8000")
   
       table = dynamodb.Table('Movies')
       table.delete()
   
   
   if __name__ == '__main__':
       delete_movie_table()
       print("Movies table deleted.")
   ```

1. To run the program, enter the following command\.

   `python MoviesDeleteTable.py`