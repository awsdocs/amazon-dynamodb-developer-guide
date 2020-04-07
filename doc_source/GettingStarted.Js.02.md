# Step 2: Load Sample Data with JavaScript into DynamoDB<a name="GettingStarted.Js.02"></a>

In this step, you populate the `Movies` table with sample data\.

**Topics**
+ [Step 2\.1: Download the Sample Data File](#GettingStarted.Js.02.01)
+ [Step 2\.2: Load the Sample Data into the Movies Table](#GettingStarted.Js.02.02)

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

## Step 2\.1: Download the Sample Data File<a name="GettingStarted.Js.02.01"></a>

1. Download the sample data archive: [moviedata\.zip](samples/moviedata.zip)

1. Extract the data file \(`moviedata.json`\) from the archive\.

1. Copy and paste the `moviedata.json` file into your current directory\.

## Step 2\.2: Load the Sample Data into the Movies Table<a name="GettingStarted.Js.02.02"></a>

After you download the sample data, you can run the following program to populate the `Movies` table\.

1. Copy the following program and paste it into a file named `MoviesLoadData.html`\.

   ```
   <!-- 
     Copyright 2010-2019 Amazon.com, Inc. or its affiliates. All Rights Reserved.
    
     This file is licensed under the Apache License, Version 2.0 (the "License").
     You may not use this file except in compliance with the License. A copy of
     the License is located at
    
     http://aws.amazon.com/apache2.0/
    
     This file is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR
     CONDITIONS OF ANY KIND, either express or implied. See the License for the
     specific language governing permissions and limitations under the License.
   -->
   <html>
   <head>
   <script src="https://sdk.amazonaws.com/js/aws-sdk-2.7.16.min.js"></script>
   
   <script type="text/javascript">
   AWS.config.update({
     region: "us-west-2",
     endpoint: 'http://localhost:8000',
     // accessKeyId default can be used while using the downloadable version of DynamoDB. 
     // For security reasons, do not store AWS Credentials in your files. Use Amazon Cognito instead.
     accessKeyId: "fakeMyKeyId",
     // secretAccessKey default can be used while using the downloadable version of DynamoDB. 
     // For security reasons, do not store AWS Credentials in your files. Use Amazon Cognito instead.
     secretAccessKey: "fakeSecretAccessKey"
   });
   
   var docClient = new AWS.DynamoDB.DocumentClient();
   
   function processFile(evt) {
       document.getElementById('textarea').innerHTML = "";
       document.getElementById('textarea').innerHTML += "Importing movies into DynamoDB. Please wait..." + "\n";
       var file = evt.target.files[0];
       if (file) {
           var r = new FileReader();
           r.onload = function(e) {
               var contents = e.target.result;
               var allMovies = JSON.parse(contents);
   
               allMovies.forEach(function (movie) {
                   document.getElementById('textarea').innerHTML += "Processing: " + movie.title + "\n";
                   var params = {
                       TableName: "Movies",
                       Item: {
                           "year": movie.year,
                           "title": movie.title,
                           "info": movie.info
                       }
                   };
                   docClient.put(params, function (err, data) {
                       if (err) {
                           document.getElementById('textarea').innerHTML += "Unable to add movie: " + count + movie.title + "\n";
                           document.getElementById('textarea').innerHTML += "Error JSON: " + JSON.stringify(err) + "\n";
                       } else {
                           document.getElementById('textarea').innerHTML += "PutItem succeeded: " + movie.title + "\n";
                           textarea.scrollTop = textarea.scrollHeight;
                       }
                   });
               });
       };
           r.readAsText(file);
       } else {
           alert("Could not read movie data file");
       }
   }
   
   </script>
   </head>
   
   <body>
   <input type="file" id="fileinput" accept='application/json'/>
   <br><br>
   <textarea readonly id= "textarea" style="width:400px; height:800px"></textarea>
   
   <script>
       document.getElementById('fileinput').addEventListener('change', processFile, false);
   </script>
   </body>
   </html>
   ```

1. Open the `MoviesLoadData.html` file in your browser\.

1. Choose **Browse**, and load the `moviedata.json` file\.