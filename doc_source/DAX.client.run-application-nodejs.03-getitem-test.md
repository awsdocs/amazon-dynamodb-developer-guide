# 03\-getitem\-test\.js<a name="DAX.client.run-application-nodejs.03-getitem-test"></a>

The `03-getitem-test.js` program performs `GetItem` operations on *TryDaxTable*\.

```
const AmazonDaxClient = require('amazon-dax-client');
var AWS = require("aws-sdk");

var region = "us-west-2";

AWS.config.update({
  region: region
});

var ddbClient = new AWS.DynamoDB.DocumentClient()
var daxClient = null;

if (process.argv.length > 2) {
    var dax = new AmazonDaxClient({endpoints: [process.argv[2]], region: region})
    daxClient = new AWS.DynamoDB.DocumentClient({service: dax });
}

var client = daxClient != null ? daxClient : ddbClient;
var tableName = "TryDaxTable";

var pk = 1;
var sk = 10;
var iterations = 5;
var promises = [];

for (var i = 0; i < iterations; i++) {

    var startTime = new Date().getTime();

    for (var ipk = 1; ipk <= pk; ipk++) {
        for (var isk = 1; isk <= sk; isk++) {

            var params = {
                TableName: tableName,
                Key:{
                    "pk": ipk,
                    "sk": isk
                }
            };

            client.get(params, function(err, data) {
                if (err) {
                    console.error("Unable to read item. Error JSON:", JSON.stringify(err, null, 2));
                } else {
                    // GetItem succeeded
                }

                remaining = remaining - 1;

                if(remaining <= 0) {
                    var endTime = new Date().getTime();
                    console.log("\tTotal time: ", (endTime - startTime) , "ms - Avg time: ", (endTime - startTime) / iterations, "ms");
                }
            });
        }
    }

}

(function wait () {
   if (remaining >= 0) setImmediate(wait);
})();
```
