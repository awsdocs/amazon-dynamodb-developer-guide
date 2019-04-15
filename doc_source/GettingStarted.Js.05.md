# Step 5: \(Optional\): Delete the Table<a name="GettingStarted.Js.05"></a>

 To delete the `Movies` table: 

1. Copy and paste the following program into a file named `MoviesDeleteTable.html`.

   ```
   <html>
   <head>
   <script src="https://sdk.amazonaws.com/js/aws-sdk-2.7.16.min.js"></script>
   
   <script>
   AWS.config.update({
     region: "us-west-2",
     endpoint: 'http://localhost:8000',
     // accessKeyId default can be used while using the downloadable version of DynamoDB. 
     // For security reasons, do not store AWS credentials in your files. Use Amazon Cognito instead.
     accessKeyId: "fakeMyKeyId",
     // secretAccessKey default can be used while using the downloadable version of DynamoDB. 
     // For security reasons, do not store AWS credentials in your files. Use Amazon Cognito instead.
     secretAccessKey: "fakeSecretAccessKey"
   });
   
   var dynamodb = new AWS.DynamoDB();
   
   function deleteMovies() {
       var params = {
           TableName : "Movies"
       };
   
       dynamodb.deleteTable(params, function(err, data) {
           if (err) {
               document.getElementById('textarea').innerHTML = "Unable to delete table: " + "\n" + JSON.stringify(err, undefined, 2);
           } else {
               document.getElementById('textarea').innerHTML = "Table deleted.";
           }
       });
   }
   
   </script>
   </head>
   
   <body>
   <input id="deleteTableButton" type="button" value="Delete Table" onclick="deleteMovies();" />
   <br><br>
   <textarea readonly id= "textarea" style="width:400px; height:800px"></textarea>
   
   </body>
   </html>
   ```

1. Open the `MoviesDeleteTable.html` file in your browser\.

1. Choose **Delete Table**\.
