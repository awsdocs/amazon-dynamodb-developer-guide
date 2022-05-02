# Query a DynamoDB table using PartiQL and an AWS SDK<a name="example_dynamodb_ExecuteStatement_section"></a>

The following code examples show how to query a DynamoDB table using PartiQL\.

**Note**  
The source code for these examples is in the [AWS Code Examples GitHub repository](https://github.com/awsdocs/aws-doc-sdk-examples)\. Have feedback on a code example? [Create an Issue](https://github.com/awsdocs/aws-doc-sdk-examples/issues/new/choose) in the code examples repo\. 

------
#### [ Java ]

**SDK for Java 2\.x**  
  

```
public class ScenarioPartiQ {

    public static void main(String [] args) throws IOException {

        final String USAGE = "\n" +
                "Usage:\n" +
                "    <fileName>\n\n" +
                "Where:\n" +
                "    fileName - The path to the moviedata.json file that you can download from the Amazon DynamoDB Developer Guide.\n" ;

          if (args.length != 1) {
                System.out.println(USAGE);
                System.exit(1);
          }

        String fileName = args[0];
        String tableName = "MoviesPartiQ";
        Region region = Region.US_EAST_1;
        DynamoDbClient ddb = DynamoDbClient.builder()
                .region(region)
                .build();

        System.out.println("******* Creating an Amazon DynamoDB table named MoviesPartiQ with a key named year and a sort key named title.");
         createTable(ddb, tableName);

        System.out.println("******* Loading data into the MoviesPartiQ table.");
        loadData(ddb, fileName);

        System.out.println("******* Getting data from the MoviesPartiQ table.");
        getItem(ddb);

        System.out.println("******* Putting a record into the MoviesPartiQ table.");
        putRecord(ddb);

        System.out.println("******* Updating a record.");
        updateTableItem(ddb);

        System.out.println("******* Querying the movies released in 2013.");
        queryTable(ddb);

        System.out.println("******* Deleting the Amazon DynamoDB table.");
        deleteDynamoDBTable(ddb, tableName);
        ddb.close();
    }
    public static void createTable(DynamoDbClient ddb, String tableName) {

        DynamoDbWaiter dbWaiter = ddb.waiter();
        ArrayList<AttributeDefinition> attributeDefinitions = new ArrayList<AttributeDefinition>();

        // Define attributes.
        attributeDefinitions.add(AttributeDefinition.builder()
                .attributeName("year")
                .attributeType("N")
                .build());

        attributeDefinitions.add(AttributeDefinition.builder()
                .attributeName("title")
                .attributeType("S")
                .build());

        ArrayList<KeySchemaElement> tableKey = new ArrayList<KeySchemaElement>();
        KeySchemaElement key = KeySchemaElement.builder()
                .attributeName("year")
                .keyType(KeyType.HASH)
                .build();

        KeySchemaElement key2 = KeySchemaElement.builder()
                .attributeName("title")
                .keyType(KeyType.RANGE) // Sort
                .build();

        // Add KeySchemaElement objects to the list.
        tableKey.add(key);
        tableKey.add(key2);

        CreateTableRequest request = CreateTableRequest.builder()
                .keySchema(tableKey)
                .provisionedThroughput(ProvisionedThroughput.builder()
                        .readCapacityUnits(new Long(10))
                        .writeCapacityUnits(new Long(10))
                        .build())
                .attributeDefinitions(attributeDefinitions)
                .tableName(tableName)
                .build();

        try {
            CreateTableResponse response = ddb.createTable(request);
            DescribeTableRequest tableRequest = DescribeTableRequest.builder()
                    .tableName(tableName)
                    .build();

            // Wait until the Amazon DynamoDB table is created.
            WaiterResponse<DescribeTableResponse> waiterResponse = dbWaiter.waitUntilTableExists(tableRequest);
            waiterResponse.matched().response().ifPresent(System.out::println);
            String newTable = response.tableDescription().tableName();
            System.out.println("The " +newTable + " was successfully created.");

        } catch (DynamoDbException e) {
            System.err.println(e.getMessage());
            System.exit(1);
        }
    }

    // Load data into the table.
    public static void loadData(DynamoDbClient ddb, String fileName) throws IOException {

        String sqlStatement = "INSERT INTO MoviesPartiQ VALUE {'year':?, 'title' : ?, 'info' : ?}";
        JsonParser parser = new JsonFactory().createParser(new File(fileName));
        com.fasterxml.jackson.databind.JsonNode rootNode = new ObjectMapper().readTree(parser);
        Iterator<JsonNode> iter = rootNode.iterator();
        ObjectNode currentNode;
        int t = 0 ;
        List<AttributeValue> parameters = new ArrayList<AttributeValue>();
        while (iter.hasNext()) {

            // Add 200 movies to the table.
            if (t == 200)
                break ;
            currentNode = (ObjectNode) iter.next();

            int year = currentNode.path("year").asInt();
            String title = currentNode.path("title").asText();
            String info = currentNode.path("info").toString();

            AttributeValue att1 = AttributeValue.builder()
                    .n(String.valueOf(year))
                    .build();

            AttributeValue att2 = AttributeValue.builder()
                    .s(title)
                    .build();

            AttributeValue att3 = AttributeValue.builder()
                    .s(info)
                    .build();

            parameters.add(att1);
            parameters.add(att2);
            parameters.add(att3);

            // Insert the movie into the Amazon DynamoDB table.
            executeStatementRequest(ddb, sqlStatement, parameters);
            System.out.println("Added Movie " +title);

            parameters.remove(att1);
            parameters.remove(att2);
            parameters.remove(att3);
            t++;
        }
    }

    public static void getItem(DynamoDbClient ddb) {

        String sqlStatement = "SELECT * FROM MoviesPartiQ where year=? and title=?";
        List<AttributeValue> parameters = new ArrayList<AttributeValue>();
        AttributeValue att1 = AttributeValue.builder()
                .n("2012")
                .build();

        AttributeValue att2 = AttributeValue.builder()
                .s("The Perks of Being a Wallflower")
                .build();

        parameters.add(att1);
        parameters.add(att2);

        try {
            ExecuteStatementResponse response = executeStatementRequest(ddb, sqlStatement, parameters);
            System.out.println("ExecuteStatement successful: "+ response.toString());

        } catch (DynamoDbException e) {
            System.err.println(e.getMessage());
            System.exit(1);
        }
    }

    public static void putRecord(DynamoDbClient ddb) {

        String sqlStatement = "INSERT INTO MoviesPartiQ VALUE {'year':?, 'title' : ?, 'info' : ?}";
        try {
            List<AttributeValue> parameters = new ArrayList<AttributeValue>();

            AttributeValue att1 = AttributeValue.builder()
                    .n(String.valueOf("2020"))
                    .build();

            AttributeValue att2 = AttributeValue.builder()
                    .s("My Movie")
                    .build();

            AttributeValue att3 = AttributeValue.builder()
                    .s("No Information")
                    .build();

            parameters.add(att1);
            parameters.add(att2);
            parameters.add(att3);

            executeStatementRequest(ddb, sqlStatement, parameters);
            System.out.println("Added new movie.");

        } catch (DynamoDbException e) {
            System.err.println(e.getMessage());
            System.exit(1);
        }
    }

    public static void updateTableItem(DynamoDbClient ddb){

        String sqlStatement = "UPDATE MoviesPartiQ SET info = 'directors\":[\"Merian C. Cooper\",\"Ernest B. Schoedsack\' where year=? and title=?";
        List<AttributeValue> parameters = new ArrayList<AttributeValue>();

        AttributeValue att1 = AttributeValue.builder()
                .n(String.valueOf("2013"))
                .build();

        AttributeValue att2 = AttributeValue.builder()
                .s("The East")
                .build();

        parameters.add(att1);
        parameters.add(att2);

        try {
            executeStatementRequest(ddb, sqlStatement, parameters);

        } catch (DynamoDbException e) {
            System.err.println(e.getMessage());
            System.exit(1);
        }
        System.out.println("Item was updated!");
    }

    // Query the table where the year is 2013.
    public static void queryTable(DynamoDbClient ddb) {
        String sqlStatement = "SELECT * FROM MoviesPartiQ where year = ?";
        try {

            List<AttributeValue> parameters = new ArrayList<AttributeValue>();

            AttributeValue att1 = AttributeValue.builder()
                    .n(String.valueOf("2013"))
                    .build();
            parameters.add(att1);

            // Get items in the table and write out the ID value.
            ExecuteStatementResponse response =  executeStatementRequest(ddb, sqlStatement, parameters);
            System.out.println("ExecuteStatement successful: "+ response.toString());

        } catch (DynamoDbException e) {
            System.err.println(e.getMessage());
            System.exit(1);
        }
    }

    public static void deleteDynamoDBTable(DynamoDbClient ddb, String tableName) {

        DeleteTableRequest request = DeleteTableRequest.builder()
                .tableName(tableName)
                .build();

        try {
            ddb.deleteTable(request);

        } catch (DynamoDbException e) {
            System.err.println(e.getMessage());
            System.exit(1);
        }
        System.out.println(tableName +" was successfully deleted!");
    }

    private static ExecuteStatementResponse executeStatementRequest(DynamoDbClient ddb, String statement, List<AttributeValue> parameters ) {
        ExecuteStatementRequest request = ExecuteStatementRequest.builder()
                .statement(statement)
                .parameters(parameters)
                .build();

        return ddb.executeStatement(request);
    }

    private static void processResults(ExecuteStatementResponse executeStatementResult) {
        System.out.println("ExecuteStatement successful: "+ executeStatementResult.toString());
    }
}
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javav2/example_code/dynamodb#readme)\. 
+  For API details, see [ExecuteStatement](https://docs.aws.amazon.com/goto/SdkForJavaV2/dynamodb-2012-08-10/ExecuteStatement) in *AWS SDK for Java 2\.x API Reference*\. 

------
#### [ JavaScript ]

**SDK for JavaScript V3**  
Create the client\.  

```
// Create the DynamoDB service client module using ES6 syntax.
import { DynamoDBClient } from "@aws-sdk/client-dynamodb";
// Set the AWS Region.
export const REGION = "eu-west-1"; // For example, "us-east-1".
// Create an Amazon DynamoDB service client object.
export const ddbClient = new DynamoDBClient({ region: REGION });
```
Create the document client\.  

```
// Create a service client module using ES6 syntax.
import { DynamoDBDocumentClient } from "@aws-sdk/lib-dynamodb";
import { ddbClient } from "./ddbClient.js";
// Set the AWS Region.
const REGION = "eu-west-1"; // For example, "us-east-1".

const marshallOptions = {
  // Whether to automatically convert empty strings, blobs, and sets to `null`.
  convertEmptyValues: false, // false, by default.
  // Whether to remove undefined values while marshalling.
  removeUndefinedValues: false, // false, by default.
  // Whether to convert typeof object to map attribute.
  convertClassInstanceToMap: false, // false, by default.
};

const unmarshallOptions = {
  // Whether to return numbers as a string instead of converting them to native JavaScript numbers.
  wrapNumbers: false, // false, by default.
};

const translateConfig = { marshallOptions, unmarshallOptions };

// Create the DynamoDB document client.
const ddbDocClient = DynamoDBDocumentClient.from(ddbClient, translateConfig);

export { ddbDocClient };
```
Query single items\.  

```
*/
import fs from "fs";
// A practical functional library used to split the data into segments.
import * as R from "ramda";
import { ddbClient } from "../libs/ddbClient.js";
import { ddbDocClient } from "../libs/ddbDocClient.js";
import { BatchWriteCommand } from "@aws-sdk/lib-dynamodb";
import {
  CreateTableCommand,
  ExecuteStatementCommand,
} from "@aws-sdk/client-dynamodb";
if (process.argv.length < 6) {
  console.log(
    "Usage: node partiQL_basics.js <tableName> <movieYear1> <movieTitle1> <producer1>\n" +
      "Example: node partiQL_basics.js Movies 2006 'The Departed' 'New View Films'"
  );
}

// Helper function to delay running the code while the AWS service calls wait for responses.
function wait(ms) {
  var start = Date.now();
  var end = start;
  while (end < start + ms) {
    end = Date.now();
  }
}

const tableName = process.argv[2];
const movieTitle1 = process.argv[3];
const movieYear1 = process.argv[4];
const producer1 = process.argv[5];

export const run = async (tableName, movieYear1, movieTitle1, producer1) => {
  try {
    console.log("Creating table ...");
    // Set the parameters.
    const params = {
      AttributeDefinitions: [
        {
          AttributeName: "title",
          AttributeType: "S",
        },
        {
          AttributeName: "year",
          AttributeType: "N",
        },
      ],
      KeySchema: [
        {
          AttributeName: "title",
          KeyType: "HASH",
        },
        {
          AttributeName: "year",
          KeyType: "RANGE",
        },
      ],
      ProvisionedThroughput: {
        ReadCapacityUnits: 5,
        WriteCapacityUnits: 5,
      },
      TableName: tableName,
    };
    const data = await ddbClient.send(new CreateTableCommand(params));
    console.log("Waiting for table to be created...");
    wait(10000);
    console.log(
      "Table created. Table name is ",
      data.TableDescription.TableName
    );
    try {
      // Before you run this example, download 'movies.json' from https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GettingStarted.Js.02.html,
      // and put it in the same folder as the example.
      // Get the movie data parse to convert into a JSON object.
      const allMovies = JSON.parse(fs.readFileSync("moviedata.json", "utf8"));
      // Split the table into segments of 25.
      const dataSegments = R.splitEvery(25, allMovies);
      // Loop batch write operation 10 times to upload 250 items.
      console.log("Writing movies in batch to table...");
      for (let i = 0; i < 10; i++) {
        const segment = dataSegments[i];
        for (let j = 0; j < 25; j++) {
          const params = {
            RequestItems: {
              [tableName]: [
                {
                  // Destination Amazon DynamoDB table name.
                  PutRequest: {
                    Item: {
                      year: segment[j].year,
                      title: segment[j].title,
                      info: segment[j].info,
                    },
                  },
                },
              ],
            },
          };
          const data = ddbDocClient.send(new BatchWriteCommand(params));
        }
      }
      wait(20000);
      console.log("Success, movies written to table.");
      try {
        const params = {
          Statement: "SELECT * FROM " + tableName + " where title=?",
          Parameters: [{ S: movieTitle1 }],
        };
        console.log("Getting movie....");

        console.log("Statement", params.Statement);
        const data = await ddbDocClient.send(
          new ExecuteStatementCommand(params)
        );
        for (let i = 0; i < data.Items.length; i++) {
          console.log(
            "Success. The query return the following data. Item " + i,
            data.Items[i].year,
            data.Items[i].title,
            data.Items[i].info
          );
        }
        try {
          const params = {
            Statement: "DELETE FROM " + tableName + " where title=? and year=?",
            Parameters: [{ S: movieTitle1 }, { N: movieYear1 }],
          };
          const data = await ddbDocClient.send(
            new ExecuteStatementCommand(params)
          );
          console.log("Success. Item deleted.");
          try {
            const params = {
              Statement:
                "INSERT INTO " + tableName + " value  {'title':?, 'year':?}",
              Parameters: [{ S: movieTitle1 }, { N: movieYear1 }],
            };
            const data = await ddbDocClient.send(
              new ExecuteStatementCommand(params)
            );
            console.log("Success. Item added.");
            try {
              const params = {
                Statement:
                  "UPDATE " +
                  tableName +
                  " SET Producer=? where title=? and year=?",
                Parameters: [
                  { S: producer1 },
                  { S: movieTitle1 },
                  { N: movieYear1 },
                ],
              };

              console.log("Updating a single movie...");
              const data = await ddbDocClient.send(
                new ExecuteStatementCommand(params)
              );
              console.log("Success. Item updated.");
              return "Run successfully"; // For unit tests.
            } catch (err) {
              console.log("Error updating item. ", err);
            }
          } catch (err) {
            console.log("Error adding items to table. ", err);
          }
        } catch (err) {
          console.log("Error deleting movie. ", err);
        }
      } catch (err) {
        console.log("Error getting movie. ", err);
      }
    } catch (err) {
      console.log("Error adding movies by batch. ", err);
    }
  } catch (err) {
    console.log("Error creating table. ", err);
  }
};
run(tableName, movieYear1, movieTitle1, producer1);
```
Query items by batch\.  

```
*/
import fs from "fs";
// A practical functional library used to split the data into segments.
import * as R from "ramda";
import { ddbClient } from "../libs/ddbClient.js";
import { ddbDocClient } from "../libs/ddbDocClient.js";
import { BatchWriteCommand } from "@aws-sdk/lib-dynamodb";
import {
  CreateTableCommand,
  BatchExecuteStatementCommand,
} from "@aws-sdk/client-dynamodb";
if (process.argv.length < 6) {
  console.log(
    "Usage: node partiQL_basics.js <tableName> <movieTitle1> <movieYear1> <movieTitle1> <movieYear1> <producer1> <producer2> \n" +
      "Example: node partiQL_basics.js Movies_batch 2006 'The Departed' 2013 '2 Guns' 'New View Films' 'Old Thyme Films'"
  );
}

const tableName = process.argv[2];
const movieYear1 = parseInt(process.argv[3]);
const movieTitle1 = process.argv[4];
const movieYear2 = parseInt(process.argv[5]);
const movieTitle2 = process.argv[6];
const producer1 = process.argv[7];
const producer2 = process.argv[8];

// Helper function to delay running the code while the AWS service calls wait for responses.
function wait(ms) {
  var start = Date.now();
  var end = start;
  while (end < start + ms) {
    end = Date.now();
  }
}
// Set the parameters.

export const run = async (
  tableName,
  movieYear1,
  movieTitle1,
  movieYear2,
  movieTitle2,
  producer1,
  producer2
) => {
  try {
    console.log("Creating table ...");
    // Set the parameters.
    const params = {
      AttributeDefinitions: [
        {
          AttributeName: "title",
          AttributeType: "S",
        },
        {
          AttributeName: "year",
          AttributeType: "N",
        },
      ],
      KeySchema: [
        {
          AttributeName: "title",
          KeyType: "HASH",
        },
        {
          AttributeName: "year",
          KeyType: "RANGE",
        },
      ],
      ProvisionedThroughput: {
        ReadCapacityUnits: 5,
        WriteCapacityUnits: 5,
      },
      TableName: tableName,
    };
    const data = await ddbClient.send(new CreateTableCommand(params));
    console.log("Waiting for table to be created...");
    wait(10000);
    console.log(
      "Table created. Table name is ",
      data.TableDescription.TableName
    );
    try {
      // Before you run this example, download 'movies.json' from https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GettingStarted.Js.02.html,
      // and put it in the same folder as the example.
      // Get the movie data parse to convert into a JSON object.
      const allMovies = JSON.parse(fs.readFileSync("moviedata.json", "utf8"));
      // Split the table into segments of 25.
      const dataSegments = R.splitEvery(25, allMovies);
      // Loop batch write operation 10 times to upload 250 items.
      console.log("Writing movies in batch to table...");
      for (let i = 0; i < 10; i++) {
        const segment = dataSegments[i];
        for (let j = 0; j < 25; j++) {
          const params = {
            RequestItems: {
              [tableName]: [
                {
                  // Destination Amazon DynamoDB table name.
                  PutRequest: {
                    Item: {
                      year: segment[j].year,
                      title: segment[j].title,
                      info: segment[j].info,
                    },
                  },
                },
              ],
            },
          };
          const data = ddbDocClient.send(new BatchWriteCommand(params));
        }
      }
      wait(10000);
      console.log("Success, movies written to table.");
      try {
        console.log("Getting movie....");
        const params = {
          Statements: [
            {
              Statement:
                "SELECT * FROM " + tableName + " where title=? and year=?",
              Parameters: [{ S: movieTitle1 }, { N: movieYear1 }],
            },
            {
              Statement:
                "SELECT * FROM " + tableName + " where title=? and year=?",
              Parameters: [{ S: movieTitle2 }, { N: movieYear2 }],
            },
          ],
        };
        const data = await ddbDocClient.send(
          new BatchExecuteStatementCommand(params)
        );
        console.log("Success. The query return the following data.", data);
        for (let i = 0; i < data.Responses.length; i++) {
          console.log(data.Responses[i].Item.year);
          console.log(data.Responses[i].Item.title);
        }
        try {
          const params = {
            Statements: [
              {
                Statement:
                  "DELETE FROM " + tableName + " where title=? and year=?",
                Parameters: [{ S: movieTitle1 }, { N: movieYear1 }],
              },
              {
                Statement:
                  "DELETE FROM " + tableName + " where title=? and year=?",
                Parameters: [{ S: movieTitle2 }, { N: movieYear2 }],
              },
            ],
          };
          const data = await ddbDocClient.send(
            new BatchExecuteStatementCommand(params)
          );
          console.log("Success. Items deleted by batch.");
          try {
            const params = {
              Statements: [
                {
                  Statement:
                    "INSERT INTO " +
                    tableName +
                    " value  {'title':?, 'year':?}",
                  Parameters: [{ S: movieTitle1 }, { N: movieYear1 }],
                },
                {
                  Statement:
                    "INSERT INTO " +
                    tableName +
                    " value  {'title':?, 'year':?}",
                  Parameters: [{ S: movieTitle2 }, { N: movieYear2 }],
                },
              ],
            };
            const data = await ddbDocClient.send(
              new BatchExecuteStatementCommand(params)
            );
            console.log("Success. Items added by batch.");
            try {
              const params = {
                Statements: [
                  {
                    Statement:
                      "UPDATE " +
                      tableName +
                      " SET Producer=? where title=? and year=?",
                    Parameters: [
                      { S: producer1 },
                      { S: movieTitle1 },
                      { N: movieYear1 },
                    ],
                  },
                  {
                    Statement:
                      "UPDATE " +
                      tableName +
                      " SET Producer=? where title=? and year=?",
                    Parameters: [
                      { S: producer2 },
                      { S: movieTitle2 },
                      { N: movieYear2 },
                    ],
                  },
                ],
              };
              console.log("Updating movies...");
              const data = await ddbDocClient.send(
                new BatchExecuteStatementCommand(params)
              );
              console.log("Success. Items updated by batch.");
              return "Run successfully"; // For unit tests.
            } catch (err) {
              console.log("Error updating items by batch. ", err);
            }
          } catch (err) {
            console.log("Error adding items to table by batch. ", err);
          }
        } catch (err) {
          console.log("Error deleting movies by batch. ", err);
        }
      } catch (err) {
        console.log("Error getting movies by batch. ", err);
      }
    } catch (err) {
      console.log("Error adding movies by batch. ", err);
    }
  } catch (err) {
    console.log("Error creating table. ", err);
  }
};
run(
  tableName,
  movieYear1,
  movieTitle1,
  movieYear2,
  movieTitle2,
  producer1,
  producer2
);
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javascriptv3/example_code/dynamodb#code-examples)\. 
+  For API details, see [ExecuteStatement](https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/clients/client-dynamodb/classes/executestatementcommand.html) in *AWS SDK for JavaScript API Reference*\. 

------
#### [ Kotlin ]

**SDK for Kotlin**  
This is prerelease documentation for a feature in preview release\. It is subject to change\.
  

```
suspend fun main(args: Array<String>) {

    val usage = """
        Usage:
          <fileName>

        Where:
           fileName - the path to the moviedata.json you can download from the Amazon DynamoDB Developer Guide.
    """

    if (args.size != 1) {
        println(usage)
        exitProcess(1)
    }

    val ddb = DynamoDbClient { region = "us-east-1" }
    val tableName = "MoviesPartiQ"

    // Get the moviedata.json from the Amazon DynamoDB Developer Guide.
    val fileName = args[0]
    println("Creating an Amazon DynamoDB table named MoviesPartiQ with a key named id and a sort key named title.")
    createTablePartiQL(ddb, tableName,"year")
    loadDataPartiQL(ddb, fileName)

    println("******* Getting data from the MoviesPartiQ table.")
    getMoviePartiQL(ddb)

   println("******* Putting a record into the MoviesPartiQ table.")
   putRecordPartiQL(ddb)

   println("******* Updating a record.")
   updateTableItemPartiQL(ddb)

   println("******* Querying the movies released in 2013.")
   queryTablePartiQL(ddb)

   println("******* Deleting the MoviesPartiQ table.")
   deleteTablePartiQL(tableName)
}

suspend fun createTablePartiQL(ddb:DynamoDbClient, tableNameVal: String, key: String) {

    val  attDef = AttributeDefinition {
        attributeName = key
        attributeType = ScalarAttributeType.N
    }

    val  attDef1 = AttributeDefinition {
        attributeName = "title"
        attributeType = ScalarAttributeType.S
    }

    val keySchemaVal =  KeySchemaElement{
        attributeName = key
        keyType = KeyType.Hash
    }

    val keySchemaVal1 =  KeySchemaElement{
        attributeName = "title"
        keyType = KeyType.Range
    }

    val provisionedVal =  ProvisionedThroughput {
        readCapacityUnits = 10
        writeCapacityUnits = 10
    }

    val request = CreateTableRequest {
        attributeDefinitions = listOf(attDef, attDef1)
        keySchema = listOf(keySchemaVal, keySchemaVal1)
        provisionedThroughput = provisionedVal
        tableName = tableNameVal
    }

    val response = ddb.createTable(request)
    ddb.waitUntilTableExists { // suspend call
         tableName = tableNameVal
    }
     println("The table was successfully created ${response.tableDescription?.tableArn}")

}

suspend fun loadDataPartiQL(ddb: DynamoDbClient, fileName: String) {
    val sqlStatement = "INSERT INTO MoviesPartiQ VALUE {'year':?, 'title' : ?, 'info' : ?}"
    val parser = JsonFactory().createParser(File(fileName))
    val rootNode = ObjectMapper().readTree<JsonNode>(parser)
    val iter: Iterator<JsonNode> = rootNode.iterator()
    var currentNode: ObjectNode
    var t = 0

    while (iter.hasNext()) {

        if (t == 200)
            break

        currentNode = iter.next() as ObjectNode
        val year = currentNode.path("year").asInt()
        val title = currentNode.path("title").asText()
        val info = currentNode.path("info").toString()

        val parameters: MutableList<AttributeValue> = ArrayList<AttributeValue>()
        parameters.add( AttributeValue.N(year.toString()))
        parameters.add(AttributeValue.S(title))
        parameters.add(AttributeValue.S(info))

        executeStatementPartiQL(ddb,sqlStatement, parameters )
        println("Added Movie $title")
        parameters.clear()
        t++
    }
}

suspend  fun getMoviePartiQL(ddb: DynamoDbClient) {
    val sqlStatement = "SELECT * FROM MoviesPartiQ where year=? and title=?"
    val parameters: MutableList<AttributeValue> = ArrayList<AttributeValue>()
    parameters.add( AttributeValue.N("2012"))
    parameters.add(AttributeValue.S("The Perks of Being a Wallflower"))
    val response = executeStatementPartiQL( ddb, sqlStatement, parameters )
    println("ExecuteStatement successful: $response")

}

suspend  fun putRecordPartiQL(ddb: DynamoDbClient) {

     val sqlStatement = "INSERT INTO MoviesPartiQ VALUE {'year':?, 'title' : ?, 'info' : ?}"
     val parameters: MutableList<AttributeValue> = java.util.ArrayList()
     parameters.add( AttributeValue.N("2020"))
     parameters.add(AttributeValue.S("My Movie"))
     parameters.add(AttributeValue.S("No Info"))
     executeStatementPartiQL(ddb, sqlStatement, parameters )
     println("Added new movie.")
}

suspend fun updateTableItemPartiQL(ddb: DynamoDbClient) {
    val sqlStatement = "UPDATE MoviesPartiQ SET info = 'directors\":[\"Merian C. Cooper\",\"Ernest B. Schoedsack\' where year=? and title=?"
    val parameters: MutableList<AttributeValue> = java.util.ArrayList()
    parameters.add( AttributeValue.N("2013"))
    parameters.add(AttributeValue.S("The East"))
    executeStatementPartiQL(ddb, sqlStatement, parameters)
    println("Item was updated!")
}

// Query the table where the year is 2013.
suspend fun queryTablePartiQL(ddb: DynamoDbClient) {
    val sqlStatement = "SELECT * FROM MoviesPartiQ where year = ?"

    val parameters: MutableList<AttributeValue> = java.util.ArrayList()
    parameters.add( AttributeValue.N("2013"))
    val response = executeStatementPartiQL(ddb, sqlStatement, parameters)
    println("ExecuteStatement successful: $response")
}

suspend fun deleteTablePartiQL(tableNameVal: String) {

    val request = DeleteTableRequest {
        tableName = tableNameVal
    }

    DynamoDbClient { region = "us-east-1" }.use { ddb ->
        ddb.deleteTable(request)
        println("$tableNameVal was deleted")
    }
}

suspend fun executeStatementPartiQL(ddb: DynamoDbClient, statementVal: String, parametersVal: List<AttributeValue>
): ExecuteStatementResponse {

    val request = ExecuteStatementRequest {
        statement = statementVal
        parameters = parametersVal
    }

    return ddb.executeStatement(request)
}
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/kotlin/services/dynamodb#code-examples)\. 
+  For API details, see [ExecuteStatement](https://github.com/awslabs/aws-sdk-kotlin#generating-api-documentation) in *AWS SDK for Kotlin API reference*\. 

------

For a complete list of AWS SDK developer guides and code examples, see [Using DynamoDB with an AWS SDK](sdk-general-information-section.md)\. This topic also includes information about getting started and details about previous SDK versions\.