# Update an item in a DynamoDB table using an AWS SDK<a name="example_dynamodb_UpdateItem_section"></a>

The following code examples show how to update an item in a DynamoDB table\.

**Note**  
The source code for these examples is in the [AWS Code Examples GitHub repository](https://github.com/awsdocs/aws-doc-sdk-examples)\. Have feedback on a code example? [Create an Issue](https://github.com/awsdocs/aws-doc-sdk-examples/issues/new/choose) in the code examples repo\. 

------
#### [ \.NET ]

**AWS SDK for \.NET**  
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/dotnetv3/dynamodb#code-examples)\. 
  

```
        /// <summary>
        /// Updates an existing item in the movies table.
        /// </summary>
        /// <param name="client">An initialized Amazon DynamoDB client object.</param>
        /// <param name="newMovie">A Movie object containing information for
        /// the movie to update.</param>
        /// <param name="newInfo">A MovieInfo object that contains the
        /// information that will be changed.</param>
        /// <param name="tableName">The name of the table that contains the movie.</param>
        /// <returns>A Boolean value that indicates the success of the operation.</returns>
        public static async Task<bool> UpdateItemAsync(
            AmazonDynamoDBClient client,
            Movie newMovie,
            MovieInfo newInfo,
            string tableName)
        {
            var key = new Dictionary<string, AttributeValue>
            {
                ["title"] = new AttributeValue { S = newMovie.Title },
                ["year"] = new AttributeValue { N = newMovie.Year.ToString() },
            };
            var updates = new Dictionary<string, AttributeValueUpdate>
            {
                ["info.plot"] = new AttributeValueUpdate
                {
                    Action = AttributeAction.PUT,
                    Value = new AttributeValue { S = newInfo.Plot },
                },

                ["info.rating"] = new AttributeValueUpdate
                {
                    Action = AttributeAction.PUT,
                    Value = new AttributeValue { N = newInfo.Rank.ToString() },
                },
            };

            var request = new UpdateItemRequest
            {
                AttributeUpdates = updates,
                Key = key,
                TableName = tableName,
            };

            var response = await client.UpdateItemAsync(request);

            return response.HttpStatusCode == System.Net.HttpStatusCode.OK;
        }
```
+  For API details, see [UpdateItem](https://docs.aws.amazon.com/goto/DotNetSDKV3/dynamodb-2012-08-10/UpdateItem) in *AWS SDK for \.NET API Reference*\. 

------
#### [ C\+\+ ]

**SDK for C\+\+**  
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/cpp/example_code/dynamodb#code-examples)\. 
  

```
//! Update an Amazon DynamoDB table item.
/*!
  \sa updateItem()
  \param tableName: The table name.
  \param partitionKey: The partition key.
  \param partitionValue: The value for the partition key.
  \param attributeKey: The key for the attribute to be updated.
  \param attributeValue: The value for the attribute to be updated.
  \param clientConfiguration: AWS client configuration.
  \return bool: Function succeeded.
  */

/*
 *  The example code only sets/updates an attribute value. It processes
 *  the attribute value as a string, even if the value could be interpreted
 *  as a number. Also, the example code does not remove an existing attribute
 *  from the key value.
 */

bool AwsDoc::DynamoDB::updateItem(const Aws::String &tableName,
                                  const Aws::String &partitionKey,
                                  const Aws::String &partitionValue,
                                  const Aws::String &attributeKey,
                                  const Aws::String &attributeValue,
                                  const Aws::Client::ClientConfiguration &clientConfiguration) {
    Aws::DynamoDB::DynamoDBClient dynamoClient(clientConfiguration);

    // *** Define UpdateItem request arguments.
    // Define TableName argument.
    Aws::DynamoDB::Model::UpdateItemRequest request;
    request.SetTableName(tableName);

    // Define KeyName argument.
    Aws::DynamoDB::Model::AttributeValue attribValue;
    attribValue.SetS(partitionValue);
    request.AddKey(partitionKey, attribValue);

    // Construct the SET update expression argument.
    Aws::String update_expression("SET #a = :valueA");
    request.SetUpdateExpression(update_expression);

    // Construct attribute name argument.
    Aws::Map<Aws::String, Aws::String> expressionAttributeNames;
    expressionAttributeNames["#a"] = attributeKey;
    request.SetExpressionAttributeNames(expressionAttributeNames);

    // Construct attribute value argument.
    Aws::DynamoDB::Model::AttributeValue attributeUpdatedValue;
    attributeUpdatedValue.SetS(attributeValue);
    Aws::Map<Aws::String, Aws::DynamoDB::Model::AttributeValue> expressionAttributeValues;
    expressionAttributeValues[":valueA"] = attributeUpdatedValue;
    request.SetExpressionAttributeValues(expressionAttributeValues);

    // Update the item.
    const Aws::DynamoDB::Model::UpdateItemOutcome &outcome = dynamoClient.UpdateItem(
            request);
    if (outcome.IsSuccess()) {
        std::cout << "Item was updated" << std::endl;
    }
    else {
        std::cerr << outcome.GetError().GetMessage() << std::endl;
    }

    return outcome.IsSuccess();
}
```
+  For API details, see [UpdateItem](https://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/UpdateItem) in *AWS SDK for C\+\+ API Reference*\. 

------
#### [ Go ]

**SDK for Go V2**  
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/gov2/dynamodb#code-examples)\. 
  

```
// TableBasics encapsulates the Amazon DynamoDB service actions used in the examples.
// It contains a DynamoDB service client that is used to act on the specified table.
type TableBasics struct {
	DynamoDbClient *dynamodb.Client
	TableName      string
}



// UpdateMovie updates the rating and plot of a movie that already exists in the
// DynamoDB table. This function uses the `expression` package to build the update
// expression.
func (basics TableBasics) UpdateMovie(movie Movie) (map[string]map[string]interface{}, error) {
	var err error
	var response *dynamodb.UpdateItemOutput
	var attributeMap map[string]map[string]interface{}
	update := expression.Set(expression.Name("info.rating"), expression.Value(movie.Info["rating"]))
	update.Set(expression.Name("info.plot"), expression.Value(movie.Info["plot"]))
	expr, err := expression.NewBuilder().WithUpdate(update).Build()
	if err != nil {
		log.Printf("Couldn't build expression for update. Here's why: %v\n", err)
	} else {
		response, err = basics.DynamoDbClient.UpdateItem(context.TODO(), &dynamodb.UpdateItemInput{
			TableName:                 aws.String(basics.TableName),
			Key:                       movie.GetKey(),
			ExpressionAttributeNames:  expr.Names(),
			ExpressionAttributeValues: expr.Values(),
			UpdateExpression:          expr.Update(),
			ReturnValues:              types.ReturnValueUpdatedNew,
		})
		if err != nil {
			log.Printf("Couldn't update movie %v. Here's why: %v\n", movie.Title, err)
		} else {
			err = attributevalue.UnmarshalMap(response.Attributes, &attributeMap)
			if err != nil {
				log.Printf("Couldn't unmarshall update response. Here's why: %v\n", err)
			}
		}
	}
	return attributeMap, err
}
```
+  For API details, see [UpdateItem](https://pkg.go.dev/github.com/aws/aws-sdk-go-v2/service/dynamodb#Client.UpdateItem) in *AWS SDK for Go API Reference*\. 

------
#### [ Java ]

**SDK for Java 2\.x**  
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javav2/example_code/dynamodb#readme)\. 
Updates an item in a table using [DynamoDbClient](http://docs.aws.amazon.com/sdk-for-java/latest/reference/software/amazon/awssdk/services/dynamodb/DynamoDbClient.html)\.  

```
    public static void updateTableItem(DynamoDbClient ddb,
                                       String tableName,
                                       String key,
                                       String keyVal,
                                       String name,
                                       String updateVal){

        HashMap<String,AttributeValue> itemKey = new HashMap<>();
        itemKey.put(key, AttributeValue.builder()
            .s(keyVal)
            .build());

        HashMap<String,AttributeValueUpdate> updatedValues = new HashMap<>();
        updatedValues.put(name, AttributeValueUpdate.builder()
            .value(AttributeValue.builder().s(updateVal).build())
            .action(AttributeAction.PUT)
            .build());

        UpdateItemRequest request = UpdateItemRequest.builder()
            .tableName(tableName)
            .key(itemKey)
            .attributeUpdates(updatedValues)
            .build();

        try {
            ddb.updateItem(request);
        } catch (ResourceNotFoundException e) {
            System.err.println(e.getMessage());
            System.exit(1);
        } catch (DynamoDbException e) {
            System.err.println(e.getMessage());
            System.exit(1);
        }
        System.out.println("The Amazon DynamoDB table was updated!");
    }
```
+  For API details, see [UpdateItem](https://docs.aws.amazon.com/goto/SdkForJavaV2/dynamodb-2012-08-10/UpdateItem) in *AWS SDK for Java 2\.x API Reference*\. 

------
#### [ JavaScript ]

**SDK for JavaScript V3**  
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javascriptv3/example_code/dynamodb#code-examples)\. 
Create the client\.  

```
// Create the DynamoDB service client module using ES6 syntax.
import { DynamoDBClient } from "@aws-sdk/client-dynamodb";
import { DEFAULT_REGION } from "../../../../libs/utils/util-aws-sdk.js";
// Create an Amazon DynamoDB service client object.
export const ddbClient = new DynamoDBClient({ region: DEFAULT_REGION });
```
Create the DynamoDB document client\.  

```
// Create a service client module using ES6 syntax.
import { DynamoDBDocumentClient } from "@aws-sdk/lib-dynamodb";
import { ddbClient } from "./ddbClient.js";

const marshallOptions = {
  // Whether to automatically convert empty strings, blobs, and sets to `null`.
  convertEmptyValues: false, // false, by default.
  // Whether to remove undefined values while marshalling.
  removeUndefinedValues: true, // false, by default.
  // Whether to convert typeof object to map attribute.
  convertClassInstanceToMap: false, // false, by default.
};

const unmarshallOptions = {
  // Whether to return numbers as a string instead of converting them to native JavaScript numbers.
  wrapNumbers: false, // false, by default.
};

// Create the DynamoDB document client.
const ddbDocClient = DynamoDBDocumentClient.from(ddbClient, {
  marshallOptions,
  unmarshallOptions,
});

export { ddbDocClient };
```
Update an item in a table using the DynamoDB document client\.  

```
import { UpdateCommand } from "@aws-sdk/lib-dynamodb";
import { ddbDocClient } from "../libs/ddbDocClient.js";

export const updateItem = async () => {
  // Set the parameters.
  const params = {
    TableName: "TABLE_NAME",
    Key: {
      title: "MOVIE_NAME",
      year: "MOVIE_YEAR",
    },
    ProjectionExpression: "#r",
    ExpressionAttributeNames: { "#r": "rank" },
    UpdateExpression: "set info.plot = :p, info.#r = :r",
    ExpressionAttributeValues: {
      ":p": "MOVIE_PLOT",
      ":r": "MOVIE_RANK",
    },
  };
  try {
    const data = await ddbDocClient.send(new UpdateCommand(params));
    console.log("Success - item added or updated", data);
    return data;
  } catch (err) {
    console.log("Error", err);
  }
};
updateItem();
```
Update an item in a table using PartiQL\.  

```
// Import required AWS SDK clients and commands for Node.js.
import { ExecuteStatementCommand } from "@aws-sdk/client-dynamodb";
import { ddbDocClient } from "../libs/ddbDocClient.js";

const tableName = process.argv[2];
const movieYear1 = process.argv[3];
const movieTitle1 = process.argv[4];
const producer1 = process.argv[5];

export const run = async (tableName, movieYear1, movieTitle1, producer1) => {
  const params = {
    Statement:
      "UPDATE " + tableName + "  SET Producer=? where title=? and year=?",
    Parameters: [{ S: producer1 }, { S: movieTitle1 }, { N: movieYear1 }],
  };
  try {
    await ddbDocClient.send(new ExecuteStatementCommand(params));
    console.log("Success. Item updated.");
    return "Run successfully"; // For unit tests.
  } catch (err) {
    console.error(err);
  }
};
run(tableName, movieYear1, movieTitle1, producer1);
```
Update items by batch in a table using PartiQL\.  

```
// Import required AWS SDK clients and commands for Node.js.
import { BatchExecuteStatementCommand } from "@aws-sdk/client-dynamodb";
import { ddbDocClient } from "../libs/ddbDocClient.js";

const tableName = process.argv[2];
const movieYear1 = process.argv[3];
const movieTitle1 = process.argv[4];
const movieYear2 = process.argv[6];
const movieTitle2 = process.argv[7];
const producer1 = process.argv[5];
const producer2 = process.argv[8];

export const run = async (
  tableName,
  movieYear1,
  movieTitle1,
  movieYear2,
  movieTitle2,
  producer1,
  producer2
) => {
  const params = {
    Statements: [
      {
        Statement:
          "UPDATE " + tableName + " SET producer=? where title=? and year=?",
        Parameters: [{ S: producer1 }, { S: movieTitle1 }, { N: movieYear1 }],
      },
      {
        Statement:
          "UPDATE " + tableName + " SET producer=? where title=? and year=?",
        Parameters: [{ S: producer2 }, { S: movieTitle2 }, { N: movieYear2 }],
      },
    ],
  };
  try {
    await ddbDocClient.send(
      new BatchExecuteStatementCommand(params)
    );
    console.log("Success. Items updated.");
    return "Run successfully"; // For unit tests.
  } catch (err) {
    console.error(err);
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
+  For API details, see [UpdateItem](https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/clients/client-dynamodb/classes/updateitemcommand.html) in *AWS SDK for JavaScript API Reference*\. 

------
#### [ Kotlin ]

**SDK for Kotlin**  
This is prerelease documentation for a feature in preview release\. It is subject to change\.
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/kotlin/services/dynamodb#code-examples)\. 
  

```
suspend fun updateTableItem(
    tableNameVal: String,
    keyName: String,
    keyVal: String,
    name: String,
    updateVal: String
) {

    val itemKey = mutableMapOf<String, AttributeValue>()
    itemKey[keyName] = AttributeValue.S(keyVal)

    val updatedValues = mutableMapOf<String, AttributeValueUpdate>()
    updatedValues[name] = AttributeValueUpdate {
        value = AttributeValue.S(updateVal)
        action = AttributeAction.Put
    }

    val request = UpdateItemRequest {
        tableName = tableNameVal
        key = itemKey
        attributeUpdates = updatedValues
    }

    DynamoDbClient { region = "us-east-1" }.use { ddb ->
        ddb.updateItem(request)
        println("Item in $tableNameVal was updated")
    }
}
```
+  For API details, see [UpdateItem](https://github.com/awslabs/aws-sdk-kotlin#generating-api-documentation) in *AWS SDK for Kotlin API reference*\. 

------
#### [ PHP ]

**SDK for PHP**  
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/php/example_code/dynamodb#code-examples)\. 
  

```
        echo "What rating would you like to give {$movie['Item']['title']['S']}?\n";
        $rating = 0;
        while (!is_numeric($rating) || intval($rating) != $rating || $rating < 1 || $rating > 10) {
            $rating = testable_readline("Rating (1-10): ");
        }
        $service->updateItemAttributeByKey($tableName, $key, 'rating', 'N', $rating);

    public function updateItemAttributeByKey(
        string $tableName,
        array $key,
        string $attributeName,
        string $attributeType,
        string $newValue
    ) {
        $this->dynamoDbClient->updateItem([
            'Key' => $key['Item'],
            'TableName' => $tableName,
            'UpdateExpression' => "set #NV=:NV",
            'ExpressionAttributeNames' => [
                '#NV' => $attributeName,
            ],
            'ExpressionAttributeValues' => [
                ':NV' => [
                    $attributeType => $newValue
                ]
            ],
        ]);
    }
```
+  For API details, see [UpdateItem](https://docs.aws.amazon.com/goto/SdkForPHPV3/dynamodb-2012-08-10/UpdateItem) in *AWS SDK for PHP API Reference*\. 

------
#### [ Python ]

**SDK for Python \(Boto3\)**  
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/python/example_code/dynamodb#code-examples)\. 
Update an item by using an update expression\.  

```
class Movies:
    """Encapsulates an Amazon DynamoDB table of movie data."""
    def __init__(self, dyn_resource):
        """
        :param dyn_resource: A Boto3 DynamoDB resource.
        """
        self.dyn_resource = dyn_resource
        self.table = None

    def update_movie(self, title, year, rating, plot):
        """
        Updates rating and plot data for a movie in the table.

        :param title: The title of the movie to update.
        :param year: The release year of the movie to update.
        :param rating: The updated rating to the give the movie.
        :param plot: The updated plot summary to give the movie.
        :return: The fields that were updated, with their new values.
        """
        try:
            response = self.table.update_item(
                Key={'year': year, 'title': title},
                UpdateExpression="set info.rating=:r, info.plot=:p",
                ExpressionAttributeValues={
                    ':r': Decimal(str(rating)), ':p': plot},
                ReturnValues="UPDATED_NEW")
        except ClientError as err:
            logger.error(
                "Couldn't update movie %s in table %s. Here's why: %s: %s",
                title, self.table.name,
                err.response['Error']['Code'], err.response['Error']['Message'])
            raise
        else:
            return response['Attributes']
```
Update an item by using an update expression that includes an arithmetic operation\.  

```
class UpdateQueryWrapper:
    def __init__(self, table):
        self.table = table

    def update_rating(self, title, year, rating_change):
        """
        Updates the quality rating of a movie in the table by using an arithmetic
        operation in the update expression. By specifying an arithmetic operation,
        you can adjust a value in a single request, rather than first getting its
        value and then setting its new value.

        :param title: The title of the movie to update.
        :param year: The release year of the movie to update.
        :param rating_change: The amount to add to the current rating for the movie.
        :return: The updated rating.
        """
        try:
            response = self.table.update_item(
                Key={'year': year, 'title': title},
                UpdateExpression="set info.rating = info.rating + :val",
                ExpressionAttributeValues={':val': Decimal(str(rating_change))},
                ReturnValues="UPDATED_NEW")
        except ClientError as err:
            logger.error(
                "Couldn't update movie %s in table %s. Here's why: %s: %s",
                title, self.table.name,
                err.response['Error']['Code'], err.response['Error']['Message'])
            raise
        else:
            return response['Attributes']
```
Update an item only when it meets certain conditions\.  

```
class UpdateQueryWrapper:
    def __init__(self, table):
        self.table = table

    def remove_actors(self, title, year, actor_threshold):
        """
        Removes an actor from a movie, but only when the number of actors is greater
        than a specified threshold. If the movie does not list more than the threshold,
        no actors are removed.

        :param title: The title of the movie to update.
        :param year: The release year of the movie to update.
        :param actor_threshold: The threshold of actors to check.
        :return: The movie data after the update.
        """
        try:
            response = self.table.update_item(
                Key={'year': year, 'title': title},
                UpdateExpression="remove info.actors[0]",
                ConditionExpression="size(info.actors) > :num",
                ExpressionAttributeValues={':num': actor_threshold},
                ReturnValues="ALL_NEW")
        except ClientError as err:
            if err.response['Error']['Code'] == "ConditionalCheckFailedException":
                logger.warning(
                    "Didn't update %s because it has fewer than %s actors.",
                    title, actor_threshold + 1)
            else:
                logger.error(
                    "Couldn't update movie %s. Here's why: %s: %s", title,
                    err.response['Error']['Code'], err.response['Error']['Message'])
            raise
        else:
            return response['Attributes']
```
+  For API details, see [UpdateItem](https://docs.aws.amazon.com/goto/boto3/dynamodb-2012-08-10/UpdateItem) in *AWS SDK for Python \(Boto3\) API Reference*\. 

------
#### [ Ruby ]

**SDK for Ruby**  
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/ruby/example_code/dynamodb#code-examples)\. 
  

```
  # Updates rating and plot data for a movie in the table.
  #
  # @param title [String] The title of the movie to update.
  # @param year [Int] The release year of the movie to update.
  # @param rating [Float] The updated rating to give the movie.
  # @param plot [String] The updated plot summary to give the movie.
  # @return [Hash] The fields that were updated, with their new values.
  def update_movie(title:, year:, rating:, plot:)
    response = @table.update_item(
      key: {"year" => year, "title" => title},
      update_expression: "set info.rating=:r, info.plot=:p",
      expression_attribute_values: { ":r" => rating, ":p" => plot },
      return_values: "UPDATED_NEW")
  rescue Aws::Errors::ServiceError => e
    puts("Couldn't update movie #{title} in table #{@table.name}. Here's why:")
    puts("\t#{e.code}: #{e.message}")
    raise
  else
    response.attributes
  end
```
+  For API details, see [UpdateItem](https://docs.aws.amazon.com/goto/SdkForRubyV3/dynamodb-2012-08-10/UpdateItem) in *AWS SDK for Ruby API Reference*\. 

------

For a complete list of AWS SDK developer guides and code examples, see [Using DynamoDB with an AWS SDK](sdk-general-information-section.md)\. This topic also includes information about getting started and details about previous SDK versions\.