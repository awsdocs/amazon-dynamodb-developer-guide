# Step 2: Load Sample Data<a name="GettingStarted.PHP.02"></a>

In this step, you populate the `Movies` table with sample data\.

**Topics**
+ [Step 2\.1: Download the Sample Data File](#GettingStarted.PHP.02.01)
+ [Step 2\.2: Load the Sample Data into the Movies Table](#GettingStarted.PHP.02.02)

This scenario uses a sample data file that contains information about a few thousand movies from the Internet Movie Database \(IMDb\)\. The movie data is in JSON format, as shown in the following example\. For each movie, there is a `year`, a `title`, and a JSON map named `info`\.

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
+ The rest of the `info` values are stored in a single attribute called `info`\. This program illustrates how you can store JSON in an Amazon DynamoDB attribute\.

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

## Step 2\.1: Download the Sample Data File<a name="GettingStarted.PHP.02.01"></a>

1. Download the sample data archive: [moviedata\.zip](samples/moviedata.zip)

1. Extract the data file \(`moviedata.json`\) from the archive\.

1. Copy and paste the `moviedata.json` file into your current directory\.

## Step 2\.2: Load the Sample Data into the Movies Table<a name="GettingStarted.PHP.02.02"></a>

After you download the sample data, you can run the following program to populate the `Movies` table\.

1. Copy the following program and paste it into a file named `MoviesLoadData.php`\.

   ```
   /**
    * Copyright 2010-2019 Amazon.com, Inc. or its affiliates. All Rights Reserved.
    *
    * This file is licensed under the Apache License, Version 2.0 (the "License").
    * You may not use this file except in compliance with the License. A copy of
    * the License is located at
    *
    * http://aws.amazon.com/apache2.0/
    *
    * This file is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR
    * CONDITIONS OF ANY KIND, either express or implied. See the License for the
    * specific language governing permissions and limitations under the License.
   */
   
   require 'vendor/autoload.php';
   
   date_default_timezone_set('UTC');
   
   use Aws\DynamoDb\Exception\DynamoDbException;
   use Aws\DynamoDb\Marshaler;
   
   $sdk = new Aws\Sdk([
       'endpoint'   => 'http://localhost:8000',
       'region'   => 'us-west-2',
       'version'  => 'latest'
   ]);
   
   $dynamodb = $sdk->createDynamoDb();
   $marshaler = new Marshaler();
   
   $tableName = 'Movies';
   
   $movies = json_decode(file_get_contents('moviedata.json'), true);
   
   foreach ($movies as $movie) {
   
       $year = $movie['year']; 
       $title = $movie['title'];
       $info = $movie['info'];
   
       $json = json_encode([
           'year' => $year,
           'title' => $title,
           'info' => $info
       ]);
   
       $params = [
           'TableName' => $tableName,
           'Item' => $marshaler->marshalJson($json)
       ];
   
       try {
           $result = $dynamodb->putItem($params);
           echo "Added movie: " . $movie['year'] . " " . $movie['title'] . "\n";
       } catch (DynamoDbException $e) {
           echo "Unable to add movie:\n";
           echo $e->getMessage() . "\n";
           break;
       }
   
   }
   ```
**Note**  
The [DynamoDB Marshaler class](https://docs.aws.amazon.com/aws-sdk-php/v3/api/class-Aws.DynamoDb.Marshaler.html) has methods for converting JSON documents and PHP arrays to the DynamoDB format\. In this program, `$marshaler->marshalJson($json)` takes a JSON document and converts it into a DynamoDB item\.

1. To run the program, enter the following command\.

   `php MoviesLoadData.php`