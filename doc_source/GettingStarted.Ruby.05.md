# Step 5: \(Optional\) Delete the Table<a name="GettingStarted.Ruby.05"></a>

 To delete the `Movies` table: 

1. Copy and paste the following program into a file named `MoviesDeleteTable.rb`:

   ```
   require "aws-sdk"
   
   Aws.config.update({
     region: "us-west-2",
     endpoint: "http://localhost:8000"
   })
   
   dynamodb = Aws::DynamoDB::Client.new
   
   params = {
       table_name: "Movies"
   }
   
   begin
       result = dynamodb.delete_table(params)
       puts "Deleted table."
   
   rescue  Aws::DynamoDB::Errors::ServiceError => error
       puts "Unable to delete table:"
       puts "#{error.message}"
   end
   ```

1. To run the program, type the following command:

   `ruby MoviesDeleteTable.rb`