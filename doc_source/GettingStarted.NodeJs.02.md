# Step 2: Load Sample Data<a name="GettingStarted.NodeJs.02"></a>

In this step, you populate the `Movies` table with sample data\.

**Topics**
+ [Step 2\.1: Download the Sample Data File](#GettingStarted.NodeJs.02.01)
+ [Step 2\.2: Load the Sample Data into the Movies Table](#GettingStarted.NodeJs.02.02)

We use a sample data file that contains information about a few thousand movies from the Internet Movie Database \(IMDb\)\. The movie data is in JSON format, as shown in the following example\. For each movie, there is a `year`, a `title`, and a JSON map named `info`\. 

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
+ The rest of the `info` values are stored in a single attribute called `info`\. This program illustrates how you can store JSON in a Amazon DynamoDB attribute\.

 The following is an example of movie data.

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

## Step 2\.1: Download the Sample Data File<a name="GettingStarted.NodeJs.02.01"></a>

1. Download the sample data archive: [moviedata\.zip](samples/moviedata.zip)

1. Extract the data file \(`moviedata.json`\) from the archive\.

1. Copy and paste the `moviedata.json` file into your current directory\.

## Step 2\.2: Load the Sample Data into the Movies Table<a name="GettingStarted.NodeJs.02.02"></a>

After you download the sample data, you can run the following program to populate the `Movies` table\.

1. Copy and paste the following program into a file named `MoviesLoadData.js`.

   ```
   var AWS = require("aws-sdk");
   var fs = require('fs');
   
   AWS.config.update({
       region: "us-west-2",
       endpoint: "http://localhost:8000"
   });
   
   var docClient = new AWS.DynamoDB.DocumentClient();
   
   console.log("Importing movies into DynamoDB. Please wait.");
   
   var allMovies = JSON.parse(fs.readFileSync('moviedata.json', 'utf8'));
   allMovies.forEach(function(movie) {
       var params = {
           TableName: "Movies",
           Item: {
               "year":  movie.year,
               "title": movie.title,
               "info":  movie.info
           }
       };
   
       docClient.put(params, function(err, data) {
          if (err) {
              console.error("Unable to add movie", movie.title, ". Error JSON:", JSON.stringify(err, null, 2));
          } else {
              console.log("PutItem succeeded:", movie.title);
          }
       });
   });
   ```

1. To run the program, type the following command.

   `node MoviesLoadData.js`
