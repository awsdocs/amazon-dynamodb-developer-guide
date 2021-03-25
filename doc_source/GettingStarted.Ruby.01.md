# Step 1: Create a Table<a name="GettingStarted.Ruby.01"></a>

In this step, you create a table named `Movies` in Amazon DynamoDB\. The primary key for the table is composed of the following two attributes:
+ `year` – The partition key\. The `attribute_type` is `N` for number\. 
+ `title` – The sort key\. The `attribute_type` is `S` for string\.

1. Copy the following program and paste it into a file named `MoviesCreateTable.rb`\.

   ```
   require 'aws-sdk-dynamodb'
   
   def create_table(dynamodb_client, table_definition)
     response = dynamodb_client.create_table(table_definition)
     response.table_description.table_status
   rescue StandardError => e
     puts "Error creating table: #{e.message}"
     'Error'
   end
   
   def run_me
     region = 'us-west-2'
   
     # To use the downloadable version of Amazon DynamoDB,
     # uncomment the endpoint statement.
     Aws.config.update(
       # endpoint: 'http://localhost:8000',
       region: region
     )
   
     dynamodb_client = Aws::DynamoDB::Client.new
   
     table_definition = {
       table_name: 'Movies',
       key_schema: [
         {
           attribute_name: 'year',
           key_type: 'HASH'  # Partition key.
         },
         {
           attribute_name: 'title',
           key_type: 'RANGE' # Sort key.
         }
       ],
       attribute_definitions: [
         {
           attribute_name: 'year',
           attribute_type: 'N'
         },
         {
           attribute_name: 'title',
           attribute_type: 'S'
         }
       ],
       provisioned_throughput: {
         read_capacity_units: 10,
         write_capacity_units: 10
       }
     }
   
     puts "Creating the table named 'Movies'..."
     create_table_result = create_table(dynamodb_client, table_definition)
   
     if create_table_result == 'Error'
       puts 'Table not created.'
     else
       puts "Table created with status '#{create_table_result}'."
     end
   end
   
   run_me if $PROGRAM_NAME == __FILE__
   ```
**Note**  
You set the endpoint to indicate that you are creating the table in the downloadable version of Amazon DynamoDB on your computer\.
In the `create_table` call, you specify table name, primary key attributes, and its data types\.
The `provisioned_throughput` parameter is required\. However, the downloadable version of DynamoDB ignores it\. \(Provisioned throughput is beyond the scope of this exercise\.\)

1. To run the program, enter the following command\.

   `ruby MoviesCreateTable.rb`

To learn more about managing tables, see [Working with Tables and Data in DynamoDB](WorkingWithTables.md)\.