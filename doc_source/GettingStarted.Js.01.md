# Step 1: Create a Table<a name="GettingStarted.Js.01"></a>

In this step, you create a table named `Movies`\. The primary key for the table is composed of the following attributes:
+ `year` – The partition key\. The `AttributeType` is `N` for number\.
+ `title` – The sort key\. The `AttributeType` is `S` for string\.

1. Copy and paste the following program into a file named `MoviesCreateTable.html`.

   ```
   <html>
   <head>
   <script src="https://sdk.amazonaws.com/js/aws-sdk-2.7.16.min.js"></script>
   
   <script>
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
   
   var dynamodb = new AWS.DynamoDB();
   
   function createMovies() {
       var params = {
           TableName : "Movies",
           KeySchema: [
               { AttributeName: "year", KeyType: "HASH"},
               { AttributeName: "title", KeyType: "RANGE" }
           ],
           AttributeDefinitions: [
               { AttributeName: "year", AttributeType: "N" },
               { AttributeName: "title", AttributeType: "S" }
           ],
           ProvisionedThroughput: {
               ReadCapacityUnits: 5,
               WriteCapacityUnits: 5
           }
       };
   
       dynamodb.createTable(params, function(err, data) {
           if (err) {
               document.getElementById('textarea').innerHTML = "Unable to create table: " + "\n" + JSON.stringify(err, undefined, 2);
           } else {
               document.getElementById('textarea').innerHTML = "Created table: " + "\n" + JSON.stringify(data, undefined, 2);
           }
       });
   }
   
   </script>
   </head>
   
   <body>
   <input id="createTableButton" type="button" value="Create Table" onclick="createMovies();" />
   <br><br>
   <textarea readonly id= "textarea" style="width:400px; height:800px"></textarea>
   
   </body>
   </html>
   ```
**Note**  
You set the endpoint to indicate that you are creating the table in Amazon DynamoDB on your computer\.
In the `createMovies` function, you specify the table name, primary key attributes, and its data types\.
The `ProvisionedThroughput` parameter is required, but the downloadable version of DynamoDB ignores it\. \(Provisioned throughput is beyond the scope of this tutorial\.\)

1. Open the `MoviesCreateTable.html` file in your browser\.

1. Choose **Create Table**\.

To learn more about managing tables, see [Working with Tables in DynamoDB](WorkingWithTables.md)\.
