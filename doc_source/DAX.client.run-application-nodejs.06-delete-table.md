# 06\-delete\-table\.js<a name="DAX.client.run-application-nodejs.06-delete-table"></a>

The `06-delete-table.js` program deletes *TryDaxTable*\. Run this program after you are done testing\.

```
const AmazonDaxClient = require('amazon-dax-client');
var AWS = require("aws-sdk");

var region = "us-west-2";

AWS.config.update({
  region: region
});

var dynamodb = new AWS.DynamoDB();  //low-level client

var tableName = "TryDaxTable";


var params = { 
    TableName : tableName
};


dynamodb.deleteTable(params, function(err, data) {
    if (err) {
        console.error("Unable to delete table. Error JSON:", JSON.stringify(err, null, 2));
    } else {
        console.log("Deleted table. Table description JSON:", JSON.stringify(data, null, 2));
    }
});
```