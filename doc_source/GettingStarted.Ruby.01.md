# Step 1: Create a Table<a name="GettingStarted.Ruby.01"></a>

In this step, you create a table named `Movies`\. The primary key for the table is composed of the following two attributes:
+ `year` – The partition key\. The `attribute_type` is `N` for number\. 
+ `title` – The sort key\. The `attribute_type` is `S` for string\.

1. Copy and paste the following program into a file named `MoviesCreateTable.rb`.

   ```
   require "aws-sdk"
   
   Aws.config.update({
     region: "us-west-2",
     endpoint: "http://localhost:8000"
   })
   
   dynamodb = Aws::DynamoDB::Client.new
   
   params = {
       table_name: "Movies",
       key_schema: [
           {
               attribute_name: "year",
               key_type: "HASH"  #Partition key
           },
           {
               attribute_name: "title",
               key_type: "RANGE" #Sort key 
           }
       ],
       attribute_definitions: [
           {
               attribute_name: "year",
               attribute_type: "N"
           },
           {
               attribute_name: "title",
               attribute_type: "S"
           },
   
       ],
       provisioned_throughput: { 
           read_capacity_units: 10,
           write_capacity_units: 10
     }
   }
   
   begin
       result = dynamodb.create_table(params)
       puts "Created table. Status: " + 
           result.table_description.table_status;
   
   rescue  Aws::DynamoDB::Errors::ServiceError => error
       puts "Unable to create table:"
       puts "#{error.message}"
   end
   ```
**Note**  
You set the endpoint to indicate that you are creating the table in the downloadable version of Amazon DynamoDB on your computer\.
In the `create_table` call, you specify the table name, primary key attributes, and its data types\.
The `provisioned_throughput` parameter is required\. However, the downloadable version of DynamoDB ignores it\. \(Provisioned throughput is beyond the scope of this exercise\.\)

1. To run the program, type the following command.

   `ruby MoviesCreateTable.rb`

To learn more about managing tables, see [Working with Tables in DynamoDB](WorkingWithTables.md)\.
