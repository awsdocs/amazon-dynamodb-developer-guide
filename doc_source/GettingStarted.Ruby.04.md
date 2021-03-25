# Step 4: Query and Scan the Data<a name="GettingStarted.Ruby.04"></a>

You can use the `query` method to retrieve data from a table\. You must specify a partition key value\. The sort key is optional\.

The primary key for the `Movies` table is composed of the following:
+ `year` – The partition key\. The attribute type is `number`\. 
+ `title` – The sort key\. The attribute type is `string`\.

To find all movies released during a year, you need to specify only the `year`\. You can also provide the `title` to retrieve a subset of movies based on some condition \(on the sort key\)\. For example, you can find movies released in 2014 that have a title starting with the letter "A"\.

In addition to the `query` method, you can use the `scan` method to retrieve all of the table data\.

To learn more about querying and scanning data, see [Working with Queries in DynamoDB](Query.md) and [Working with Scans in DynamoDB](Scan.md), respectively\. 

**Topics**
+ [Step 4\.1: Query \- All Movies Released in a Year](#GettingStarted.Ruby.04.Query.01)
+ [Step 4\.2: Query \- All Movies Released in a Year with Certain Titles](#GettingStarted.Ruby.04.Query.02)
+ [Step 4\.3: Scan](#GettingStarted.Ruby.04.Scan)

## Step 4\.1: Query \- All Movies Released in a Year<a name="GettingStarted.Ruby.04.Query.01"></a>

The following program retrieves all movies released in the `year` 1985\.

1. Copy the following program and paste it into a file named `MoviesQuery01.rb`\.

   ```
   require 'aws-sdk-dynamodb'
   
   def query_for_items_from_table(dynamodb_client, query_condition)
     # To display the elapsed time for the query operation,
     # uncomment the following three comments.
     # start = Time.now
     result = dynamodb_client.query(query_condition)
     # finish = Time.now
     # puts "Search took #{finish - start} seconds."
     if result.items.count.zero?
       puts 'No matching movies found.'
     else
       puts "Found #{result.items.count} matching movies:"
       result.items.each do |movie|
         puts "#{movie['title']} (#{movie['year'].to_i})"
       end
     end
   rescue StandardError => e
     puts "Error querying for table items: #{e.message}"
   end
   
   def run_me
     region = 'us-west-2'
     table_name = 'Movies'
     year = 1985
   
     # To use the downloadable version of Amazon DynamoDB,
     # uncomment the endpoint statement.
     Aws.config.update(
       # endpoint: 'http://localhost:8000',
       region: region
     )
   
     dynamodb_client = Aws::DynamoDB::Client.new
   
     # To query on the 'title' range/sort key in addition to the 'year'
     # hash/partition key, uncomment the following three 'title' comments.
     query_condition = {
       table_name: table_name,
       key_condition_expression: '#yr = :yyyy', # '#yr = :yyyy AND #t = :title' 
       expression_attribute_names: {
         # '#t' => 'title',
         '#yr' => 'year'
       },
       expression_attribute_values: {
         # ':title' => 'After Hours',
         ':yyyy' => year
       }
     }
   
     puts "Searching for items in the '#{table_name}' table from '#{year}'..."
   
     query_for_items_from_table(dynamodb_client, query_condition)
   end
   
   run_me if $PROGRAM_NAME == __FILE__
   ```
**Note**  
`expression_attribute_names` provides name substitution\. We use this because `year` is a reserved word in Amazon DynamoDB\. You can't use it directly in any expression, including `KeyConditionExpression`\. You can use the expression attribute name `#yr` to address this\.
`expression_attribute_values` provides value substitution\. You use this because you can't use literals in any expression, including `key_condition_expression`\. You can use the expression attribute value `:yyyy` to address this\.

1. To run the program, enter the following command\.

   `ruby MoviesItemQuery01.rb`

**Note**  
The preceding program shows how to query a table by its primary key attributes\. In DynamoDB, you can optionally create one or more secondary indexes on a table and query those indexes in the same way that you query a table\. Secondary indexes give your applications additional flexibility by allowing queries on non\-key attributes\. For more information, see [Improving Data Access with Secondary Indexes](SecondaryIndexes.md)\. 

## Step 4\.2: Query \- All Movies Released in a Year with Certain Titles<a name="GettingStarted.Ruby.04.Query.02"></a>

The following program retrieves all movies released in `year` 1992 with a `title` beginning with the letter "A" through the letter "L"\.

1. Copy the following program and paste it into a file named `MoviesQuery02.rb`\.

   ```
   require 'aws-sdk-dynamodb'
   
   def query_for_items_from_table(dynamodb_client, query_condition)
     # To display the elapsed time for the query operation,
     # uncomment the following three comments.
     # start = Time.now
     result = dynamodb_client.query(query_condition)
     # finish = Time.now
     # puts "Search took #{finish - start} seconds."
     if result.items.count.zero?
       puts 'No matching movies found.'
     else
       puts "Found #{result.items.count} matching movies:"
       result.items.each do |movie|
         puts "#{movie['title']} (#{movie['year'].to_i}):"
         if movie['info'].key?('genres') && movie['info']['genres'].count.positive?
           puts '  Genres:'
           movie['info']['genres'].each do |genre|
             puts "    #{genre}"
           end
         end
         if movie['info'].key?('actors') && movie['info']['actors'].count.positive?
           puts '  Actors:'
           movie['info']['actors'].each do |actor|
             puts "    #{actor}"
           end
         end
       end
     end
   rescue StandardError => e
     puts "Error querying for table items: #{e.message}"
   end
   
   def run_me
     region = 'us-west-2'
     table_name = 'Movies'
     year = 1982
     letter1 = 'A'
     letter2 = 'L'
   
     # To use the downloadable version of Amazon DynamoDB,
     # uncomment the endpoint statement.
     Aws.config.update(
       # endpoint: 'http://localhost:8000',
       region: region
     )
   
     dynamodb_client = Aws::DynamoDB::Client.new
   
     query_condition = {
       table_name: table_name,
       projection_expression: '#yr, title, info.genres, info.actors[0]',
       key_condition_expression: '#yr = :yyyy AND title BETWEEN :letter1 AND :letter2',
       expression_attribute_names: { '#yr' => 'year' },
       expression_attribute_values: {
         ':yyyy' => year,
         ':letter1' => letter1,
         ':letter2' => letter2
       }
     }
   
     puts "Searching for items in the '#{table_name}' table from '#{year}' and " \
       "titles starting with the letters '#{letter1}' through '#{letter2}'..."
   
     query_for_items_from_table(dynamodb_client, query_condition)
   end
   
   run_me if $PROGRAM_NAME == __FILE__
   ```

1. To run the program, enter the following command\.

   `ruby MoviesQuery02.rb`

## Step 4\.3: Scan<a name="GettingStarted.Ruby.04.Scan"></a>

The `scan` method reads every item in the entire table and returns all the data in the table\. You can provide an optional `filter_expression` so that only the items matching your criteria are returned\. However, note that the filter is only applied after the entire table has been scanned\.

The following program scans the `Movies` table, which contains approximately 5,000 items\. The scan specifies the optional filter to retrieve only the movies from the 1950s \(approximately 100 items\) and discard all the others\. 

1. Copy the following program and paste it into a file named `MoviesScan.rb`\.

   ```
   require 'aws-sdk-dynamodb'
   
   def scan_for_items_from_table(dynamodb_client, scan_condition)
     # To display the elapsed time for the query operation,
     # uncomment the following three comments.
     #start = Time.now
     loop do
       result = dynamodb_client.scan(scan_condition)
   
       if result.items.count.zero?
         puts 'No matching movies found (yet)...'
       else
         puts "Found #{result.items.count} matching movies (so far):"
         result.items.each do |movie|
           puts "#{movie["title"]} (#{movie["year"].to_i}), " \
             "Rating: #{movie["info"]["rating"].to_f}"
         end
           
         break if result.last_evaluated_key.nil?
   
         puts "Searching for more movies..."
         scan_condition[:exclusive_start_key] = result.last_evaluated_key
       end
     end
     puts 'Finished searching.'
     # finish = Time.now
     # puts "Search took #{finish - start} seconds."
   rescue StandardError => e
     puts "Error scanning for table items: #{e.message}"
   end
   
   def run_me
     region = 'us-west-2'
     table_name = 'Movies'
     start_year = 1950
     end_year = 1959
   
     # To use the downloadable version of Amazon DynamoDB,
     # uncomment the endpoint statement.
     Aws.config.update(
       # endpoint: 'http://localhost:8000',
       region: region
     )
   
     dynamodb_client = Aws::DynamoDB::Client.new
         
     scan_condition = {
       table_name: table_name,
       projection_expression: '#yr, title, info.rating',
       filter_expression: '#yr between :start_yr and :end_yr',
       expression_attribute_names: { '#yr' => 'year' },
       expression_attribute_values: {
         ':start_yr' => start_year,
         ':end_yr' => end_year
       }
     }
   
     puts "Searching for items in the '#{table_name}' table from #{start_year} " \
       "through #{end_year}..."
   
     scan_for_items_from_table(dynamodb_client, scan_condition)  
   end
   
   run_me if $PROGRAM_NAME == __FILE__
   ```

   In the code, note the following:
   + `projection_expression` specifies the attributes you want in the scan result\.
   + `filter_expression` specifies a condition that returns only items that satisfy the condition\. All other items are discarded\.

1. To run the program, enter the following command\.

   `ruby MoviesScan.rb`

**Note**  
You can also use the `scan` method with any secondary indexes that you create on the table\. For more information, see [Improving Data Access with Secondary Indexes](SecondaryIndexes.md)\. 