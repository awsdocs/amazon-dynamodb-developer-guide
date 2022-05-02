# Query a DynamoDB table using an AWS SDK<a name="example_dynamodb_Query_section"></a>

The following code examples show how to query a DynamoDB table\.

**Note**  
The source code for these examples is in the [AWS Code Examples GitHub repository](https://github.com/awsdocs/aws-doc-sdk-examples)\. Have feedback on a code example? [Create an Issue](https://github.com/awsdocs/aws-doc-sdk-examples/issues/new/choose) in the code examples repo\. 

------
#### [ C\+\+ ]

**SDK for C\+\+**  
  

```
        Aws::Client::ClientConfiguration clientConfig;
       
        Aws::DynamoDB::DynamoDBClient dynamoClient(clientConfig);
        Aws::DynamoDB::Model::QueryRequest req;
        
        req.SetTableName(table);

        // Set query key condition expression
        req.SetKeyConditionExpression(partitionKeyAttributeName + "= :valueToMatch");

        // Set Expression AttributeValues
        Aws::Map<Aws::String, Aws::DynamoDB::Model::AttributeValue> attributeValues;
        attributeValues.emplace(":valueToMatch", partitionKeyAttributeValue);

        req.SetExpressionAttributeValues(attributeValues);

        // Perform Query operation
        const Aws::DynamoDB::Model::QueryOutcome& result = dynamoClient.Query(req);
        if (result.IsSuccess())
        {
            // Reference the retrieved items
            const Aws::Vector<Aws::Map<Aws::String, Aws::DynamoDB::Model::AttributeValue>>& items = result.GetResult().GetItems();
            if(items.size() > 0) 
            {
                std::cout << "Number of items retrieved from Query: " << items.size() << std::endl;
                //Iterate each item and print
                for(const auto &item: items)
                {
                std::cout << "******************************************************" << std::endl;
                // Output each retrieved field and its value
                for (const auto& i : item)
                    std::cout << i.first << ": " << i.second.GetS() << std::endl;
                }
            }

            else
            {
                std::cout << "No item found in table: " << table << std::endl;
            }
        }
        else
        {
            std::cout << "Failed to Query items: " << result.GetError().GetMessage();
        }
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/cpp/example_code/dynamodb#code-examples)\. 
+  For API details, see [Query](https://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/Query) in *AWS SDK for C\+\+ API Reference*\. 

------
#### [ Go ]

**SDK for Go V2**  
  

```
// TableBasics encapsulates the Amazon DynamoDB service actions used in the examples.
// It contains a DynamoDB service client that is used to act on the specified table.
type TableBasics struct {
	DynamoDbClient *dynamodb.Client
	TableName      string
}



// Query gets all movies in the DynamoDB table that were released in the specified year.
// The function uses the `expression` package to build the key condition expression
// that is used in the query.
func (basics TableBasics) Query(releaseYear int) ([]Movie, error) {
	var err error
	var response *dynamodb.QueryOutput
	var movies []Movie
	keyEx := expression.Key("year").Equal(expression.Value(releaseYear))
	expr, err := expression.NewBuilder().WithKeyCondition(keyEx).Build()
	if err != nil {
		log.Printf("Couldn't build epxression for query. Here's why: %v\n", err)
	} else {
		response, err = basics.DynamoDbClient.Query(context.TODO(), &dynamodb.QueryInput{
			TableName:                 aws.String(basics.TableName),
			ExpressionAttributeNames:  expr.Names(),
			ExpressionAttributeValues: expr.Values(),
			KeyConditionExpression:    expr.KeyCondition(),
		})
		if err != nil {
			log.Printf("Couldn't query for movies released in %v. Here's why: %v\n", releaseYear, err)
		} else {
			err = attributevalue.UnmarshalListOfMaps(response.Items, &movies)
			if err != nil {
				log.Printf("Couldn't unmarshal query response. Here's why: %v\n", err)
			}
		}
	}
	return movies, err
}
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/gov2/dynamodb#code-examples)\. 
+  For API details, see [Query](https://pkg.go.dev/github.com/aws/aws-sdk-go-v2/service/dynamodb#Client.Query) in *AWS SDK for Go API Reference*\. 

------
#### [ Java ]

**SDK for Java 2\.x**  
Queries a table by using the enhanced client\.  

```
    public static String queryTable(DynamoDbEnhancedClient enhancedClient) {

        try{
            DynamoDbTable<Customer> mappedTable = enhancedClient.table("Customer", TableSchema.fromBean(Customer.class));
            QueryConditional queryConditional = QueryConditional
                    .keyEqualTo(Key.builder()
                            .partitionValue("id120")
                            .build());

            // Get items in the table and write out the ID value
            Iterator<Customer> results = mappedTable.query(queryConditional).items().iterator();
            String result="";

            while (results.hasNext()) {
                Customer rec = results.next();
                result = rec.getId();
                System.out.println("The record id is "+result);
            }
            return result;

        } catch (DynamoDbException e) {
            System.err.println(e.getMessage());
            System.exit(1);
        }
        return "";
    }
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javav2/example_code/dynamodb#readme)\. 
+  For API details, see [Query](https://docs.aws.amazon.com/goto/SdkForJavaV2/dynamodb-2012-08-10/Query) in *AWS SDK for Java 2\.x API Reference*\. 

------
#### [ JavaScript ]

**SDK for JavaScript V3**  
Create the client\.  

```
// Create service client module using ES6 syntax.
import { DynamoDBClient } from "@aws-sdk/client-dynamodb";
// Set the AWS Region.
const REGION = "REGION"; //e.g. "us-east-1"
// Create an Amazon DynamoDB service client object.
const ddbClient = new DynamoDBClient({ region: REGION });
export { ddbClient };
```
Query the table\.  

```
// Import required AWS SDK clients and commands for Node.js
import { QueryCommand } from "@aws-sdk/client-dynamodb";
import { ddbClient } from "./libs/ddbClient.js";

// Set the parameters
export const params = {
  KeyConditionExpression: "Season = :s and Episode > :e",
  FilterExpression: "contains (Subtitle, :topic)",
  ExpressionAttributeValues: {
    ":s": { N: "1" },
    ":e": { N: "2" },
    ":topic": { S: "SubTitle" },
  },
  ProjectionExpression: "Episode, Title, Subtitle",
  TableName: "EPISODES_TABLE",
};

export const run = async () => {
  try {
    const data = await ddbClient.send(new QueryCommand(params));
    return data;
    data.Items.forEach(function (element, index, array) {
      console.log(element.Title.S + " (" + element.Subtitle.S + ")");
    });
  } catch (err) {
    console.error(err);
  }
};
run();
```
Create the client for the DynamoDB document client\.  

```
// Create service client module using ES6 syntax.
import { DynamoDBDocumentClient} from "@aws-sdk/lib-dynamodb";
import {ddbClient} from "./ddbClient";

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

// Create the DynamoDB Document client.
const ddbDocClient = DynamoDBDocumentClient.from(ddbClient, translateConfig);

export { ddbDocClient };
```
Query the table using the DynamoDB document client\.  

```
import { QueryCommand } from "@aws-sdk/lib-dynamodb";
import { ddbDocClient } from "../libs/ddbDocClient.js";

// Set the parameters.
export const params = {
  ExpressionAttributeNames: { "#r": "rank", "#y": "year" },
  ProjectionExpression: "#r, #y, title",
  TableName: "TABLE_NAME",
  ExpressionAttributeValues: {
    ":t": "MOVIE_NAME",
    ":y": "MOVIE_YEAR",
    ":r": "MOVIE_RANK",
  },
  KeyConditionExpression: "title = :t and #y = :y",
  FilterExpression: "info.#r = :r",
};

export const queryTable = async () => {
  try {
    const data = await ddbDocClient.send(new QueryCommand(params));
    for (let i = 0; i < data.Items.length; i++) {
      console.log(
        "Success. Items with rank of " +
          "MOVIE_RANK" +
          " include\n" +
          "Year = " +
          data.Items[i].year +
          " Title = " +
          data.Items[i].title
      );
    }
  } catch (err) {
    console.log("Error", err);
  }
};
queryTable();
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javascriptv3/example_code/dynamodb#code-examples)\. 
+  For more information, see [AWS SDK for JavaScript Developer Guide](https://docs.aws.amazon.com/sdk-for-javascript/v3/developer-guide/dynamodb-example-query-scan.html#dynamodb-example-table-query-scan-querying)\. 
+  For API details, see [Query](https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/clients/client-dynamodb/classes/querycommand.html) in *AWS SDK for JavaScript API Reference*\. 

**SDK for JavaScript V2**  
  

```
// Load the AWS SDK for Node.js
var AWS = require('aws-sdk');
// Set the region 
AWS.config.update({region: 'REGION'});

// Create DynamoDB document client
var docClient = new AWS.DynamoDB.DocumentClient({apiVersion: '2012-08-10'});

var params = {
  ExpressionAttributeValues: {
    ':s': 2,
    ':e': 9,
    ':topic': 'PHRASE'
   },
 KeyConditionExpression: 'Season = :s and Episode > :e',
 FilterExpression: 'contains (Subtitle, :topic)',
 TableName: 'EPISODES_TABLE'
};

docClient.query(params, function(err, data) {
  if (err) {
    console.log("Error", err);
  } else {
    console.log("Success", data.Items);
  }
});
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javascript/example_code/dynamodb#code-examples)\. 
+  For more information, see [AWS SDK for JavaScript Developer Guide](https://docs.aws.amazon.com/sdk-for-javascript/v2/developer-guide/dynamodb-example-query-scan.html#dynamodb-example-table-query-scan-querying)\. 
+  For API details, see [Query](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/dynamodb-2012-08-10/Query) in *AWS SDK for JavaScript API Reference*\. 

------
#### [ Kotlin ]

**SDK for Kotlin**  
This is prerelease documentation for a feature in preview release\. It is subject to change\.
  

```
suspend fun queryDynTable(
        tableNameVal: String,
        partitionKeyName: String,
        partitionKeyVal: String,
        partitionAlias: String
    ): Int {

        val attrNameAlias = mutableMapOf<String, String>()
        attrNameAlias[partitionAlias] = partitionKeyName

        // Set up mapping of the partition name with the value.
        val attrValues = mutableMapOf<String, AttributeValue>()
        attrValues[":$partitionKeyName"] = AttributeValue.S(partitionKeyVal)

        val request = QueryRequest {
            tableName = tableNameVal
            keyConditionExpression = "$partitionAlias = :$partitionKeyName"
            expressionAttributeNames = attrNameAlias
            this.expressionAttributeValues = attrValues
        }

        DynamoDbClient { region = "us-east-1" }.use { ddb ->
            val response = ddb.query(request)
            return response.count
        }
  }
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/kotlin/services/dynamodb#code-examples)\. 
+  For API details, see [Query](https://github.com/awslabs/aws-sdk-kotlin#generating-api-documentation) in *AWS SDK for Kotlin API reference*\. 

------
#### [ Python ]

**SDK for Python \(Boto3\)**  
Query items by using a key condition expression\.  

```
class Movies:
    """Encapsulates an Amazon DynamoDB table of movie data."""
    def __init__(self, dyn_resource):
        """
        :param dyn_resource: A Boto3 DynamoDB resource.
        """
        self.dyn_resource = dyn_resource
        self.table = None

    def query_movies(self, year):
        """
        Queries for movies that were released in the specified year.

        :param year: The year to query.
        :return: The list of movies that were released in the specified year.
        """
        try:
            response = self.table.query(KeyConditionExpression=Key('year').eq(year))
        except ClientError as err:
            logger.error(
                "Couldn't query for movies released in %s. Here's why: %s: %s", year,
                err.response['Error']['Code'], err.response['Error']['Message'])
            raise
        else:
            return response['Items']
```
Query items and project them to return a subset of data\.  

```
class UpdateQueryWrapper:
    def __init__(self, table):
        self.table = table

    def query_and_project_movies(self, year, title_bounds):
        """
        Query for movies that were released in a specified year and that have titles
        that start within a range of letters. A projection expression is used
        to return a subset of data for each movie.

        :param year: The release year to query.
        :param title_bounds: The range of starting letters to query.
        :return: The list of movies.
        """
        try:
            response = self.table.query(
                ProjectionExpression="#yr, title, info.genres, info.actors[0]",
                ExpressionAttributeNames={"#yr": "year"},
                KeyConditionExpression=(
                    Key('year').eq(year) &
                    Key('title').between(title_bounds['first'], title_bounds['second'])))
        except ClientError as err:
            if err.response['Error']['Code'] == "ValidationException":
                logger.warning(
                    "There's a validation error. Here's the message: %s: %s",
                    err.response['Error']['Code'], err.response['Error']['Message'])
            else:
                logger.error(
                    "Couldn't query for movies. Here's why: %s: %s",
                    err.response['Error']['Code'], err.response['Error']['Message'])
                raise
        else:
            return response['Items']
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/python/example_code/dynamodb#code-examples)\. 
+  For API details, see [Query](https://docs.aws.amazon.com/goto/boto3/dynamodb-2012-08-10/Query) in *AWS SDK for Python \(Boto3\) API Reference*\. 

------
#### [ Ruby ]

**SDK for Ruby**  
  

```
  # Queries for movies that were released in the specified year.
  #
  # @param year [Integer] The year to query.
  # @return [Array] The list of movies that were released in the specified year.
  def query_movies(year)
    response = @table.query(
      key_condition_expression: "#yr = :year",
      expression_attribute_names: {"#yr" => "year"},
      expression_attribute_values: {":year" => year})
  rescue Aws::Errors::ServiceError => e
    puts("Couldn't query for movies released in #{year}. Here's why:")
    puts("\t#{e.code}: #{e.message}")
    raise
  else
    response.items
  end
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/ruby/example_code/dynamodb#code-examples)\. 
+  For API details, see [Query](https://docs.aws.amazon.com/goto/SdkForRubyV3/dynamodb-2012-08-10/Query) in *AWS SDK for Ruby API Reference*\. 

------
#### [ Rust ]

**SDK for Rust**  
This documentation is for an SDK in preview release\. The SDK is subject to change and should not be used in production\.
Find the movies made in the specified year\.  

```
fn movies_in_year(client: &Client, table_name: &str, year: u16) -> Query {
    client
        .query()
        .table_name(table_name)
        .key_condition_expression("#yr = :yyyy")
        .expression_attribute_names("#yr", "year")
        .expression_attribute_values(":yyyy", AttributeValue::N(year.to_string()))
}
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/rust_dev_preview/dynamodb#code-examples)\. 
+  For API details, see [Query](https://docs.rs/releases/search?query=aws-sdk) in *AWS SDK for Rust API reference*\. 

------

For a complete list of AWS SDK developer guides and code examples, see [Using DynamoDB with an AWS SDK](sdk-general-information-section.md)\. This topic also includes information about getting started and details about previous SDK versions\.