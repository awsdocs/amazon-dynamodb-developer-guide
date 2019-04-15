# Step 1: Create a Table<a name="GettingStarted.NodeJs.01"></a>

In this step, you create a table named `Movies`\. The primary key for the table is composed of the following attributes:
+ `year` – The partition key\. The `AttributeType` is `N` for number\.
+ `title` – The sort key\. The `AttributeType` is `S` for string\.

1. Copy and paste the following program into a file named `MoviesCreateTable.js`\.

   ```
   var AWS = require("aws-sdk");
   
   AWS.config.update({
     region: "us-west-2",
     endpoint: "http://localhost:8000"
   });
   
   var dynamodb = new AWS.DynamoDB();
   
   var params = {
       TableName : "Movies",
       KeySchema: [       
           { AttributeName: "year", KeyType: "HASH"},  //Partition key
           { AttributeName: "title", KeyType: "RANGE" }  //Sort key
       ],
       AttributeDefinitions: [       
           { AttributeName: "year", AttributeType: "N" },
           { AttributeName: "title", AttributeType: "S" }
       ],
       ProvisionedThroughput: {       
           ReadCapacityUnits: 10, 
           WriteCapacityUnits: 10
       }
   };
   
   dynamodb.createTable(params, function(err, data) {
       if (err) {
           console.error("Unable to create table. Error JSON:", JSON.stringify(err, null, 2));
       } else {
           console.log("Created table. Table description JSON:", JSON.stringify(data, null, 2));
       }
   });
   ```
**Note**  
You set the endpoint to indicate that you are creating the table in Amazon DynamoDB on your computer\.
In the `createTable` call, you specify the table name, primary key attributes, and its data types\.
The `ProvisionedThroughput` parameter is required, but the downloadable version of DynamoDB ignores it\. \(Provisioned throughput is beyond the scope of this tutorial\.\)

1. To run the program, type the following command.

   `node MoviesCreateTable.js`

To learn more about managing tables, see [Working with Tables in DynamoDB](WorkingWithTables.md)\.
