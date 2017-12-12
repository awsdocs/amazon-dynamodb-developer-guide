# Step 4: Query and Scan the Data<a name="GettingStarted.Ruby.04"></a>

You can use the `query` method to retrieve data from a table\. You must specify a partition key value\. The sort key is optional\.

The primary key for the `Movies` table is composed of the following:

+ `year` – The partition key\. The attribute type is number\. 

+ `title` – The sort key\. The attribute type is string\.

To find all movies released during a year, you need to specify only the `year`\. You can also provide the `title` to retrieve a subset of movies based on some condition \(on the sort key\); for example, to find movies released in 2014 that have a title starting with the letter "A"\.

In addition to `query`, there is also a `scan` method that can retrieve all of the table data\.

To learn more about querying and scanning data, see [Working with Queries](Query.md) and [Working with Scans](Scan.md), respectively\. 


+ [Step 4\.1: Query \- All Movies Released in a Year](#GettingStarted.Ruby.04.Query.01)
+ [Step 4\.2: Query \- All Movies Released in a Year with Certain Titles](#GettingStarted.Ruby.04.Query.02)
+ [Step 4\.3: Scan](#GettingStarted.Ruby.04.Scan)

## Step 4\.1: Query \- All Movies Released in a Year<a name="GettingStarted.Ruby.04.Query.01"></a>

The following program retrieves all movies released in the `year` 1985\.

1. Copy and paste the following program into a file named `MoviesQuery01.rb`:

   ```
   require "aws-sdk"
   
   Aws.config.update({
     region: "us-west-2",
     endpoint: "http://localhost:8000"
   })
   
   dynamodb = Aws::DynamoDB::Client.new
   
   tableName = "Movies"
   
   params = {
       table_name: tableName,
       key_condition_expression: "#yr = :yyyy",
       expression_attribute_names: {
           "#yr" => "year"
       },
       expression_attribute_values: {
           ":yyyy" => 1985 
       }
   }
   
   puts "Querying for movies from 1985.";
   
   begin
       result = dynamodb.query(params)
       puts "Query succeeded."
       
       result.items.each{|movie|
            puts "#{movie["year"].to_i} #{movie["title"]}"
       }
   
   rescue  Aws::DynamoDB::Errors::ServiceError => error
       puts "Unable to query table:"
       puts "#{error.message}"
   end
   ```
**Note**  
`expression_attribute_names` provides name substitution\. We use this because `year` is a reserved word in DynamoDB—you can't use it directly in any expression, including `KeyConditionExpression`\. You can use the expression attribute name `#yr` to address this\.
`expression_attribute_values` provides value substitution\. You use this because you can't use literals in any expression, including `key_condition_expression`\. You can use the expression attribute value `:yyyy` to address this\.

1. To run the program, type the following command:

   `ruby MoviesItemQuery01.rb`

**Note**  
The preceding program shows how to query a table by its primary key attributes\. In DynamoDB, you can optionally create one or more secondary indexes on a table, and query those indexes in the same way that you query a table\. Secondary indexes give your applications additional flexibility by allowing queries on non\-key attributes\. For more information, see [Improving Data Access with Secondary Indexes](SecondaryIndexes.md)\. 

## Step 4\.2: Query \- All Movies Released in a Year with Certain Titles<a name="GettingStarted.Ruby.04.Query.02"></a>

The following program retrieves all movies released in `year` 1992, with a `title` beginning with the letter "A" through the letter "L"\.

1. Copy and paste the following program into a file named `MoviesQuery02.rb`:

   ```
   require "aws-sdk"
   
   Aws.config.update({
     region: "us-west-2",
     endpoint: "http://localhost:8000"
   })
   
   dynamodb = Aws::DynamoDB::Client.new
   
   tableName = "Movies"
   
   params = {
       table_name: tableName,
       projection_expression: "#yr, title, info.genres, info.actors[0]",
       key_condition_expression: 
           "#yr = :yyyy and title between :letter1 and :letter2",
       expression_attribute_names: {
           "#yr" => "year"
       },
       expression_attribute_values: {
           ":yyyy" => 1992,
           ":letter1" => "A",
           ":letter2" => "L"
       }
   }
   
   puts "Querying for movies from 1992 - titles A-L, with genres and lead actor";
   
   begin
       result = dynamodb.query(params)
       puts "Query succeeded."
       
       result.items.each{|movie|
            print "#{movie["year"].to_i}: #{movie["title"]} ... "
   
            movie['info']['genres'].each{|gen| 
               print gen + " "
            }
           
            print " ... #{movie["info"]["actors"][0]}\n"
       }
   
   rescue  Aws::DynamoDB::Errors::ServiceError => error
       puts "Unable to query table:"
       puts "#{error.message}"
   end
   ```

1. To run the program, type the following command:

   `ruby MoviesQuery02.rb`

## Step 4\.3: Scan<a name="GettingStarted.Ruby.04.Scan"></a>

The `scan` method reads every item in the entire table, and returns all the data in the table\. You can provide an optional `filter_expression`, so that only the items matching your criteria are returned\. However, note that the filter is only applied after the entire table has been scanned\.

The following program scans the `Movies` table, which contains approximately 5,000 items\. The scan specifies the optional filter to retrieve only the movies from the 1950s \(approximately 100 items\), and discard all the others\. 

1. Copy and paste the following program into a file named `MoviesScan.rb`:

   ```
   require "aws-sdk"
   
   Aws.config.update({
     region: "us-west-2",
     endpoint: "http://localhost:8000"
   })
   
   dynamodb = Aws::DynamoDB::Client.new
   
   tableName = "Movies"
   
   params = {
       table_name: tableName,
       projection_expression: "#yr, title, info.rating",
       filter_expression: "#yr between :start_yr and :end_yr",
       expression_attribute_names: {"#yr"=> "year"},
       expression_attribute_values: {
           ":start_yr" => 1950,
           ":end_yr" => 1959
       }
   }
   
   puts "Scanning Movies table."
   
   begin
       loop do
           result = dynamodb.scan(params)
   
           result.items.each{|movie|
               puts "#{movie["year"].to_i}: " +
                   "#{movie["title"]} ... " + 
                   "#{movie["info"]["rating"].to_f}"
           }
           
           break if result.last_evaluated_key.nil?
   
           puts "Scanning for more..."
           params[:exclusive_start_key] = result.last_evaluated_key
       end
       
   rescue  Aws::DynamoDB::Errors::ServiceError => error
       puts "Unable to scan:"
       puts "#{error.message}"
   end
   ```

   In the code, note the following:

   + `projection_expression` specifies the attributes you want in the scan result\.

   + `filter_expression` specifies a condition that returns only items that satisfy the condition\. All other items are discarded\.

1. To run the program, type the following command:

   `ruby MoviesScan.rb`

**Note**  
You can also use the `scan` method with any secondary indexes that you create on the table\. For more information, see [Improving Data Access with Secondary Indexes](SecondaryIndexes.md)\. 