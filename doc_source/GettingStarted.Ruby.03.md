# Step 3: Create, Read, Update, and Delete an Item<a name="GettingStarted.Ruby.03"></a>

In this step, you perform read and write operations on an item in the `Movies` table in Amazon DynamoDB\.

To learn more about reading and writing data, see [Working with Items and Attributes](WorkingWithItems.md)\.

**Topics**
+ [Step 3\.1: Create a New Item](#GettingStarted.Ruby.03.01)
+ [Step 3\.2: Read an Item](#GettingStarted.Ruby.03.02)
+ [Step 3\.3: Update an Item](#GettingStarted.Ruby.03.03)
+ [Step 3\.4: Increment an Atomic Counter](#GettingStarted.Ruby.03.04)
+ [Step 3\.5: Update an Item \(Conditionally\)](#GettingStarted.Ruby.03.05)
+ [Step 3\.6: Delete an Item](#GettingStarted.Ruby.03.06)

## Step 3\.1: Create a New Item<a name="GettingStarted.Ruby.03.01"></a>

In this step, you add a new item to the table\.

1. Copy the following program and paste it into a file named `MoviesItemOps01.rb`\.

   ```
   require 'aws-sdk-dynamodb'
   
   def add_item_to_table(dynamodb_client, table_item)
     dynamodb_client.put_item(table_item)
     puts "Added movie '#{table_item[:item][:title]} " \
       "(#{table_item[:item][:year]})'."
   rescue StandardError => e
     puts "Error adding movie '#{table_item[:item][:title]} " \
       "(#{table_item[:item][:year]})': #{e.message}"
   end
   
   def run_me
     region = 'us-west-2'
     table_name = 'Movies'
     title = 'The Big New Movie'
     year = 2015
   
     # To use the downloadable version of Amazon DynamoDB,
     # uncomment the endpoint statement.
     Aws.config.update(
       # endpoint: 'http://localhost:8000',
       region: region
     )
   
     dynamodb_client = Aws::DynamoDB::Client.new
   
     item = {
       year: year,
       title: title,
       info: {
         plot: 'Nothing happens at all.',
         rating: 0
       }
     }
   
     table_item = {
       table_name: table_name,
       item: item
     }
   
     puts "Adding movie '#{item[:title]} (#{item[:year]})' " \
       "to table '#{table_name}'..."
     add_item_to_table(dynamodb_client, table_item)
   end
   
   run_me if $PROGRAM_NAME == __FILE__
   ```
**Note**  
The primary key is required\. This code adds an item that has primary key \(`year`, `title`\) and `info` attributes\. The `info` attribute stores a map that provides more information about the movie\.

1. To run the program, enter the following command\.

   `ruby MoviesItemOps01.rb`

## Step 3\.2: Read an Item<a name="GettingStarted.Ruby.03.02"></a>

In the previous program, you added the following item to the table\.

```
{
   year: 2015,
   title: "The Big New Movie",
   info: { 
        plot: "Nothing happens at all.",
        rating: 0
   }
}
```

You can use the `get_item` method to read the item from the `Movies` table\. You must specify the primary key values so that you can read any item from `Movies` if you know its `year` and `title`\.

1. Copy the following program and paste it into a file named `MoviesItemOps02.rb`\.

   ```
   require 'aws-sdk-dynamodb'
   
   def get_item_from_table(dynamodb_client, table_item)
     result = dynamodb_client.get_item(table_item)
     puts "#{result.item['title']} (#{result.item['year'].to_i}):"
     puts "  Plot:   #{result.item['info']['plot']}"
     puts "  Rating: #{result.item['info']['rating'].to_i}"
   rescue StandardError => e
     puts "Error getting movie '#{table_item[:key][:title]} " \
           "(#{table_item[:key][:year]})': #{e.message}"
   end
   
   def run_me
     region = 'us-west-2'
     table_name = 'Movies'
     title = 'The Big New Movie'
     year = 2015
   
     # To use the downloadable version of Amazon DynamoDB,
     # uncomment the endpoint statement.
     Aws.config.update(
       # endpoint: 'http://localhost:8000',
       region: region
     )
   
     dynamodb_client = Aws::DynamoDB::Client.new
   
     table_item = {
       table_name: table_name,
       key: {
         year: year,
         title: title
       }
     }
   
     puts "Getting information about '#{title} (#{year})' " \
       "from table '#{table_name}'..."
     get_item_from_table(dynamodb_client, table_item)
   end
   
   run_me if $PROGRAM_NAME == __FILE__
   ```

1. To run the program, enter the following command\.

   `ruby MoviesItemOps02.rb`

## Step 3\.3: Update an Item<a name="GettingStarted.Ruby.03.03"></a>

You can use the `update_item` method to modify an existing item\. You can update values of existing attributes, add new attributes, or remove attributes\.

In this example, you perform the following updates:
+ Change the value of the existing attributes \(`rating`, `plot`\)\. 
+ Add a new list attribute \(`actors`\) to the existing `info` map\.

The following shows the existing item\.

```
{
   year: 2015,
   title: "The Big New Movie",
   info: { 
        plot: "Nothing happens at all.",
        rating: 0
   }
}
```

The item is updated as follows\.

```
{
   year: 2015,
   title: "The Big New Movie",
   info: { 
           plot: "Everything happens all at once.",
           rating: 5.5,
           actors: ["Larry", "Moe", "Curly"]
   }
}
```

1. Copy the following program and paste it into a file named `MoviesItemOps03.rb`\.

   ```
   require 'aws-sdk-dynamodb'
   
   def table_item_updated?(dynamodb_client, table_item)
     response = dynamodb_client.update_item(table_item)
     puts "Table item updated with the following attributes for 'info':"
     response.attributes['info'].each do |key, value|
       if key == 'rating'
         puts "#{key}: #{value.to_f}"
       else
         puts "#{key}: #{value}"
       end
     end
     true
   rescue StandardError => e
     puts "Error updating item: #{e.message}"
     false
   end
   
   def run_me
     region = 'us-west-2'
     table_name = 'Movies'
     title = 'The Big New Movie'
     year = 2015
   
     # To use the downloadable version of Amazon DynamoDB,
     # uncomment the endpoint statement.
     Aws.config.update(
       # endpoint: 'http://localhost:8000',
       region: region
     )
   
     dynamodb_client = Aws::DynamoDB::Client.new
   
     table_item = {
       table_name: table_name,
       key: {
         year: year,
         title: title
       },
       update_expression: 'SET info.rating = :r, info.plot = :p, info.actors = :a',
       expression_attribute_values: {
         ':r': 5.5,
         ':p': 'Everything happens all at once.',
         ':a': [ 'Larry', 'Moe', 'Curly' ]
       },
       return_values: 'UPDATED_NEW'
     }
   
     puts "Updating table '#{table_name}' with information about " \
       "'#{title} (#{year})'..."
   
     if table_item_updated?(dynamodb_client, table_item)
       puts 'Table updated.'
     else
       puts 'Table not updated.'
     end
   end
   
   run_me if $PROGRAM_NAME == __FILE__
   ```
**Note**  
This program uses `update_expression` to describe all updates you want to perform on the specified item\.  
The `return_values` parameter instructs Amazon DynamoDB to return only the updated attributes \(`UPDATED_NEW`\)\.

1. To run the program, enter the following command\.

   `ruby MoviesItemOps03.rb`

## Step 3\.4: Increment an Atomic Counter<a name="GettingStarted.Ruby.03.04"></a>

DynamoDB supports atomic counters, which use the `update_item` method to increment or decrement the value of an existing attribute without interfering with other write requests\. \(All write requests are applied in the order in which they are received\.\)

The following program shows how to increment the `rating` for a movie\. Each time you run the program, it increments this attribute by one\. 

1. Copy the following program and paste it into a file named `MoviesItemOps04.rb`\.

   ```
   require 'aws-sdk-dynamodb'
   
   def table_item_updated?(dynamodb_client, table_item)
     result = dynamodb_client.update_item(table_item)
     puts "Table item updated with the following attributes for 'info':"
     result.attributes['info'].each do |key, value|
       if key == 'rating'
         puts "#{key}: #{value.to_f}"
       else
         puts "#{key}: #{value}"
       end
     end
     true
   rescue StandardError => e
     puts "Error updating item: #{e.message}"
     false
   end
   
   def run_me
     region = 'us-west-2'
     table_name = 'Movies'
     title = 'The Big New Movie'
     year = 2015
   
     # To use the downloadable version of Amazon DynamoDB,
     # uncomment the endpoint statement.
     Aws.config.update(
       # endpoint: 'http://localhost:8000',
       region: region
     )
   
     dynamodb_client = Aws::DynamoDB::Client.new
   
     table_item = {
       table_name: table_name,
       key: {
         year: year,
         title: title
       },
       update_expression: 'SET info.rating = info.rating + :val',
       expression_attribute_values: {
         ':val': 1
       },
       return_values: 'UPDATED_NEW'
     }
   
     puts "Updating table '#{table_name}' with information about " \
       "'#{title} (#{year})'..."
   
     if table_item_updated?(dynamodb_client, table_item)
       puts 'Table updated.'
     else
       puts 'Table not updated.'
     end
   end
   
   run_me if $PROGRAM_NAME == __FILE__
   ```

1. To run the program, enter the following command\.

   `ruby MoviesItemOps04.rb`

## Step 3\.5: Update an Item \(Conditionally\)<a name="GettingStarted.Ruby.03.05"></a>

The following program shows how to use `update_item` with a condition\. If the condition evaluates to true, the update succeeds; otherwise, the update is not performed\. 

In this case, the item is updated only if the number of actors is greater than three\.

1. Copy the following program and paste it into a file named `MoviesItemOps05.rb`\.

   ```
   require 'aws-sdk-dynamodb'
   
   def table_item_updated?(dynamodb_client, table_item)
     result = dynamodb_client.update_item(table_item)
     puts "Table item updated with the following attributes for 'info':"
     result.attributes['info'].each do |key, value|
       if key == 'rating'
         puts "#{key}: #{value.to_f}"
       else
         puts "#{key}: #{value}"
       end
     end
     true
   rescue StandardError => e
     puts "Error updating item: #{e.message}"
     false
   end
   
   def run_me
     region = 'us-west-2'
     table_name = 'Movies'
     title = 'The Big New Movie'
     year = 2015
   
     # To use the downloadable version of Amazon DynamoDB,
     # uncomment the endpoint statement.
     Aws.config.update(
       # endpoint: 'http://localhost:8000',
       region: region
     )
   
     dynamodb_client = Aws::DynamoDB::Client.new
   
     table_item = {
       table_name: table_name,
       key: {
         year: year,
         title: title
       },
       update_expression: 'REMOVE info.actors[0]',
       condition_expression: 'size(info.actors) > :num',
       expression_attribute_values: {
         ':num': 3
       },
       return_values: 'UPDATED_NEW'
     }
   
     puts "Updating table '#{table_name}' with information about " \
       "'#{title} (#{year})'..."
   
     if table_item_updated?(dynamodb_client, table_item)
       puts 'Table updated.'
     else
       puts 'Table not updated.'
     end
   end
   
   run_me if $PROGRAM_NAME == __FILE__
   ```

1. To run the program, enter the following command\.

   `ruby MoviesItemOps05.rb`

   The program should fail with the following message\.

   The conditional request failed

   The program fails because the movie has three actors in it, but the condition is checking for *greater than* three actors\.

1. Modify the program so that the `ConditionExpression` looks like the following\.

   ```
   condition_expression: "size(info.actors) >= :num", 
   ```

   The condition is now *greater than or equal to 3* instead of *greater than 3*\.

1. Run the program again\. The `update_item` method should now succeed\.

## Step 3\.6: Delete an Item<a name="GettingStarted.Ruby.03.06"></a>

You can use the `delete_item` method to delete one item by specifying its primary key\. You can optionally provide a `condition_expression` to prevent the item from being deleted if the condition is not met\.

In the following example, you try to delete a specific movie item if its rating is 5 or less\.

1. Copy the following program and paste it into a file named `MoviesItemOps06.rb`\.

   ```
   require 'aws-sdk-dynamodb'
   
   def table_item_deleted?(dynamodb_client, table_item)
     dynamodb_client.delete_item(table_item)
     true
   rescue StandardError => e
     puts "Error deleting item: #{e.message}"
     false
   end
   
   def run_me
     region = 'us-west-2'
     table_name = 'Movies'
     title = 'The Big New Movie'
     year = 2015
   
     # To use the downloadable version of Amazon DynamoDB,
     # uncomment the endpoint statement.
     Aws.config.update(
       # endpoint: 'http://localhost:8000',
       region: region
     )
   
     dynamodb_client = Aws::DynamoDB::Client.new
   
     table_item = {
       table_name: table_name,
       key: {
         year: year,
         title: title
       },
       condition_expression: 'info.rating <= :val',
       expression_attribute_values: {
         ':val' => 5
       }
     }
   
     puts "Deleting item from table '#{table_name}' matching " \
       "'#{title} (#{year})' if specified criteria are met..."
   
     if table_item_deleted?(dynamodb_client, table_item)
       puts 'Item deleted.'
     else
       puts 'Item not deleted.'
     end
   end
   
   run_me if $PROGRAM_NAME == __FILE__
   ```

1. To run the program, enter the following command\.

   `ruby MoviesItemOps06.rb`

   The program should fail with the following message\.

   The conditional request failed

   The program fails because the rating for this particular move is greater than 5\.

1. Modify the program to remove the condition\.

   ```
   params = {
       table_name: "Movies",
       key: {
           year: year,
           title: title
       }
   }
   ```

1. Run the program\. Now, the delete succeeds because you removed the condition\.