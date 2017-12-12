# 01\-create\-table\.js<a name="DAX.client.run-application-nodejs.01-create-table"></a>

The `01-create-table.js` program creates a table \(*TryDaxTable*\)\. The remaining Node\.js programs in this section depend on this table\.

```
const AmazonDaxClient = require('amazon-dax-client');
var AWS = require("aws-sdk");

var region = "us-west-2";

AWS.config.update({
  region: region
});

var dynamodb = new AWS.DynamoDB() //low-level client

var tableName = "TryDaxTable";

var params = {
    TableName : tableName,
    KeySchema: [       
        { AttributeName: "pk", KeyType: "HASH"},  //Partition key
        { AttributeName: "sk", KeyType: "RANGE" }  //Sort key
    ],
    AttributeDefinitions: [       
        { AttributeName: "pk", AttributeType: "N" },
        { AttributeName: "sk", AttributeType: "N" }
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