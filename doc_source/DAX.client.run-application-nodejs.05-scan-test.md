# 05\-scan\-test\.js<a name="DAX.client.run-application-nodejs.05-scan-test"></a>

The `05-scan-test.js` program performs `Scan` operations on *TryDaxTable*\.

```
const AmazonDaxClient = require('amazon-dax-client');
var AWS = require("aws-sdk");

var region = "us-west-2";

AWS.config.update({
  region: region
});

var client = new AWS.DynamoDB.DocumentClient()

if (process.argv.length > 2) {
    var dax = new AmazonDaxClient({endpoints: [process.argv[2]], region: region})
    client = new AWS.DynamoDB.DocumentClient({service: dax });
}

var tableName = "TryDaxTable";

var iterations = 5;
var remaining = iterations;

var params = {
    TableName: tableName
};

for (var i = 0; i < iterations; i++) {
    var startTime = new Date().getTime();

    client.scan(params, function(err, data) {
        if (err) {
            console.error("Unable to read item. Error JSON:", JSON.stringify(err, null, 2));
        } else {
            // Scan succeeded
        }

        remaining = remaining - 1;

        if(remaining <= 0) {
            var endTime = new Date().getTime();
            console.log("\tTotal time: ", (endTime - startTime) , "ms - Avg time: ", (endTime - startTime) / iterations, "ms");
        }
    });

}

(function wait () {
   if (remaining >= 0) setImmediate(wait);
})();
```
