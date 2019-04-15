# Step 5: \(Optional\): Delete the Table<a name="GettingStarted.NodeJs.05"></a>

 To delete the `Movies` table: 

1. Copy and paste the following program into a file named `MoviesDeleteTable.js`.

   ```
   var AWS = require("aws-sdk");
   
   AWS.config.update({
     region: "us-west-2",
     endpoint: "http://localhost:8000"
   });
   
   var dynamodb = new AWS.DynamoDB();
   
   var params = {
       TableName : "Movies"
   };
   
   dynamodb.deleteTable(params, function(err, data) {
       if (err) {
           console.error("Unable to delete table. Error JSON:", JSON.stringify(err, null, 2));
       } else {
           console.log("Deleted table. Table description JSON:", JSON.stringify(data, null, 2));
       }
   });
   ```

1. To run the program, type the following command.

   `node MoviesDeleteTable.js`
