# Step 2: Load Sample Data<a name="GettingStarted.Ruby.02"></a>

In this step, you populate the `Movies` table in Amazon DynamoDB with sample data\.

**Topics**
+ [Step 2\.1: Download the Sample Data File](#GettingStarted.Ruby.02.01)
+ [Step 2\.2: Load the Sample Data into the Movies Table](#GettingStarted.Ruby.02.02)

You use a sample data file that contains information about a few thousand movies from the Internet Movie Database \(IMDb\)\. The movie data is in JSON format, as shown in the following example\. For each movie, there is a `year`, a `title`, and a JSON map named `info`\.

```
[
   {
      "year" : ... ,
      "title" : ... ,
      "info" : { ... }
   },
   {
      "year" : ...,
      "title" : ...,
      "info" : { ... }
   },

    ...

]
```

In the JSON data, note the following:
+ The `year` and `title` are used as the primary key attribute values for the `Movies` table\.
+ You store the rest of the `info` values in a single attribute called `info`\. This program illustrates how you can store JSON in a DynamoDB attribute\.

 The following is an example of movie data\.

```
{
    "year" : 2013,
    "title" : "Turn It Down, Or Else!",
    "info" : {
        "directors" : [
            "Alice Smith",
            "Bob Jones"
        ],
        "release_date" : "2013-01-18T00:00:00Z",
        "rating" : 6.2,
        "genres" : [
            "Comedy",
            "Drama"
        ],
        "image_url" : "http://ia.media-imdb.com/images/N/O9ERWAU7FS797AJ7LU8HN09AMUP908RLlo5JF90EWR7LJKQ7@@._V1_SX400_.jpg",
        "plot" : "A rock band plays their music at high volumes, annoying the neighbors.",
        "rank" : 11,
        "running_time_secs" : 5215,
        "actors" : [
            "David Matthewman",
            "Ann Thomas",
            "Jonathan G. Neff"
       ]
    }
}
```

## Step 2\.1: Download the Sample Data File<a name="GettingStarted.Ruby.02.01"></a>

1. Download the sample data archive: [moviedata\.zip](samples/moviedata.zip)

1. Extract the data file \(`moviedata.json`\) from the archive\.

1. Copy the `moviedata.json` file and paste it into your current directory\.

## Step 2\.2: Load the Sample Data into the Movies Table<a name="GettingStarted.Ruby.02.02"></a>

After you download the sample data, run the following program to populate the `Movies` table\.

1. Copy the following program and paste it into a file named `MoviesLoadData.rb`\.

   ```
   require 'aws-sdk-dynamodb'
   require 'json'
   
   $movie_counter = 0
   $total_movies = 0
   
   def add_item_to_table(dynamodb_client, table_item)
     dynamodb_client.put_item(table_item)
     $movie_counter += 1
     puts "Added movie #{$movie_counter}/#{$total_movies}: " \
       "'#{table_item[:item]['title']} " \
       "(#{table_item[:item]['year']})'."
   rescue StandardError => e
     puts "Error adding movie '#{table_item[:item]['title']} " \
       "(#{table_item[:item]['year']})': #{e.message}"
     puts 'Program stopped.'
     exit 1
   end
   
   def run_me
     region = 'us-west-2'
     table_name = 'Movies'
     data_file = 'moviedata.json'
   
     # To use the downloadable version of Amazon DynamoDB,
     # uncomment the endpoint statement.
     Aws.config.update(
       # endpoint: 'http://localhost:8000',
       region: region
     )
   
     dynamodb_client = Aws::DynamoDB::Client.new
     file = File.read(data_file)
     movies = JSON.parse(file)
     $total_movies = movies.count
   
     puts "Adding #{$total_movies} movies from file '#{data_file}' " \
       "into table '#{table_name}'..."
   
     movies.each do |movie|
       table_item = {
         table_name: table_name,
         item: movie
       }
       add_item_to_table(dynamodb_client, table_item)
     end
   
     puts 'Done.'
   end
   
   run_me if $PROGRAM_NAME == __FILE__
   ```

1. To run the program, enter the following command\.

   `ruby MoviesLoadData.rb`