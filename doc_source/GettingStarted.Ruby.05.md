# Step 5: \(Optional\) Delete the Table<a name="GettingStarted.Ruby.05"></a>

 Follow these steps to delete the `Movies` table\.

1. Copy the following program and paste it into a file named `MoviesDeleteTable.rb`\.

   ```
   require 'aws-sdk-dynamodb'
   
   def table_deleted?(dynamodb_client, table_name)
     dynamodb_client.delete_table(table_name: table_name)
     true
   rescue StandardError => e
     puts "Error deleting table: #{e.message}"
     false
   end
   
   def run_me
     region = 'us-west-2'
     table_name = 'Movies'
   
     # To use the downloadable version of Amazon DynamoDB,
     # uncomment the endpoint statement.
     Aws.config.update(
       # endpoint: 'http://localhost:8000',
       region: region
     )
     
     dynamodb_client = Aws::DynamoDB::Client.new
   
     puts "Deleting table '#{table_name}'..."
   
     if table_deleted?(dynamodb_client, table_name)
       puts 'Table deleted.'
     else
       puts 'Table not deleted.'
     end
   end
   
   run_me if $PROGRAM_NAME == __FILE__
   ```

1. To run the program, enter the following command\.

   `ruby MoviesDeleteTable.rb`