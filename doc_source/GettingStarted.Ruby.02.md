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
   #
   #  Copyright 2010-2019 Amazon.com, Inc. or its affiliates. All Rights Reserved.
   #
   #  This file is licensed under the Apache License, Version 2.0 (the "License").
   #  You may not use this file except in compliance with the License. A copy of
   #  the License is located at
   # 
   #  http://aws.amazon.com/apache2.0/
   # 
   #  This file is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR
   #  CONDITIONS OF ANY KIND, either express or implied. See the License for the
   #  specific language governing permissions and limitations under the License.
   #
   require "aws-sdk"
   require "json"
   
   Aws.config.update({
     region: "us-west-2",
     endpoint: "http://localhost:8000"
   })
   
   dynamodb = Aws::DynamoDB::Client.new
   
   table_name = 'Movies'
   
   file = File.read('moviedata.json')
   movies = JSON.parse(file)
   movies.each{|movie|
   
       params = {
           table_name: table_name,
           item: movie
       }
   
       begin
           dynamodb.put_item(params)
           puts "Added movie: #{movie["year"]} #{movie["title"]}"
   
       rescue  Aws::DynamoDB::Errors::ServiceError => error
           puts "Unable to add movie:"
           puts "#{error.message}"
       end
   }
   ```

1. To run the program, enter the following command\.

   `ruby MoviesLoadData.rb`