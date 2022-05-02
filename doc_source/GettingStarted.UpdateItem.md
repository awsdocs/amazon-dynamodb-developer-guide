# Update an item in a DynamoDB table<a name="GettingStarted.UpdateItem"></a>

You can update items from DynamoDB tables using the AWS Management Console, the AWS CLI, or an AWS SDK\. For more information on items, see [Core Components of Amazon DynamoDB](HowItWorks.CoreComponents.md)\.

## Update an item in a DynamoDB table using an AWS SDK<a name="GettingStarted.UpdateItem.SDK"></a>

The following code examples show how to update an item in a DynamoDB table using an AWS SDK\.

------
#### [ C\+\+ ]

**SDK for C\+\+**  
  

```
        Aws::Client::ClientConfiguration clientConfig;
        Aws::DynamoDB::DynamoDBClient dynamoClient(clientConfig);

        // *** Define UpdateItem request arguments
        // Define TableName argument.
        Aws::DynamoDB::Model::UpdateItemRequest request;
        request.SetTableName(tableName);

        // Define KeyName argument.
        Aws::DynamoDB::Model::AttributeValue attribValue;
        attribValue.SetS(keyValue);
        request.AddKey("id", attribValue);

        // Construct the SET update expression argument.
        Aws::String update_expression("SET #a = :valueA");
        request.SetUpdateExpression(update_expression);

        // Parse the attribute name and value. Syntax: "name=value".
        auto parsed = Aws::Utils::StringUtils::Split(attributeNameAndValue, '=');
        
        if (parsed.size() != 2)
        {
            std::cout << "Invalid argument syntax: " << attributeNameAndValue << USAGE;
            return 1;
        }

        // Construct attribute name argument
        // Note: Setting the ExpressionAttributeNames argument is required only
        // when the name is a reserved word, such as "default". Otherwise, the 
        // name can be included in the update_expression, as in 
        // "SET MyAttributeName = :valueA"
        Aws::Map<Aws::String, Aws::String> expressionAttributeNames;
        expressionAttributeNames["#a"] = parsed[0];
        request.SetExpressionAttributeNames(expressionAttributeNames);

        // Construct attribute value argument.
        Aws::DynamoDB::Model::AttributeValue attributeUpdatedValue;
        attributeUpdatedValue.SetS(parsed[1]);
        Aws::Map<Aws::String, Aws::DynamoDB::Model::AttributeValue> expressionAttributeValues;
        expressionAttributeValues[":valueA"] = attributeUpdatedValue;
        request.SetExpressionAttributeValues(expressionAttributeValues);

        // Update the item.
        const Aws::DynamoDB::Model::UpdateItemOutcome& result = dynamoClient.UpdateItem(request);
        if (!result.IsSuccess())
        {
            std::cout << result.GetError().GetMessage() << std::endl;
            return 1;
        }
        std::cout << "Item was updated" << std::endl;
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/cpp/example_code/dynamodb#code-examples)\. 
+  For API details, see [UpdateItem](https://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/UpdateItem) in *AWS SDK for C\+\+ API Reference*\. 

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
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/gov2/dynamodb#code-examples)\. 
+  For API details, see [UpdateItem](https://pkg.go.dev/github.com/aws/aws-sdk-go-v2/service/dynamodb#Client.UpdateItem) in *AWS SDK for Go API Reference*\. 

------
#### [ Java ]

**SDK for Java 2\.x**  
Updates an item located in a table by using the enhanced client\.  

```
    public static String modifyItem(DynamoDbEnhancedClient enhancedClient, String keyVal, String email) {
        try {
            //Create a DynamoDbTable object
            DynamoDbTable<Customer> mappedTable = enhancedClient.table("Customer", TableSchema.fromBean(Customer.class));

            //Create a KEY object
            Key key = Key.builder()
                    .partitionValue(keyVal)
                    .build();

            // Get the item by using the key and update the email value.
            Customer customerRec = mappedTable.getItem(r->r.key(key));
            customerRec.setEmail(email);
            mappedTable.updateItem(customerRec);
            return customerRec.getEmail();

        } catch (DynamoDbException e) {
            System.err.println(e.getMessage());
            System.exit(1);
        }
        return "";
    }
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javav2/example_code/dynamodb#readme)\. 
+  For API details, see [UpdateItem](https://docs.aws.amazon.com/goto/SdkForJavaV2/dynamodb-2012-08-10/UpdateItem) in *AWS SDK for Java 2\.x API Reference*\. 

------
#### [ JavaScript ]

**SDK for JavaScript V3**  
Create the client\.  

```
// Create the DynamoDB service client module using ES6 syntax.
import { DynamoDBClient } from "@aws-sdk/client-dynamodb";
// Set the AWS Region.
export const REGION = "REGION"; // For example, "us-east-1".
// Create an Amazon DynamoDB service client object.
export const ddbClient = new DynamoDBClient({ region: REGION });
```
Create the DynamoDB document client\.  

```
// Create a service client module using ES6 syntax.
import { DynamoDBDocumentClient } from "@aws-sdk/lib-dynamodb";
import { ddbClient } from "./ddbClient.js";
// Set the AWS Region.
const REGION = "REGION"; // For example, "us-east-1".

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
    const data = await ddbDocClient.send(new ExecuteStatementCommand(params));
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
    const data = await ddbDocClient.send(
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
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javascriptv3/example_code/dynamodb#code-examples)\. 
+  For API details, see [UpdateItem](https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/clients/client-dynamodb/classes/updateitemcommand.html) in *AWS SDK for JavaScript API Reference*\. 

------
#### [ Kotlin ]

**SDK for Kotlin**  
This is prerelease documentation for a feature in preview release\. It is subject to change\.
  

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
            attributeUpdates= updatedValues
        }

        DynamoDbClient { region = "us-east-1" }.use { ddb ->
            ddb.updateItem(request)
            println("Item in $tableNameVal was updated")
        }
}
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/kotlin/services/dynamodb#code-examples)\. 
+  For API details, see [UpdateItem](https://github.com/awslabs/aws-sdk-kotlin#generating-api-documentation) in *AWS SDK for Kotlin API reference*\. 

------
#### [ Python ]

**SDK for Python \(Boto3\)**  
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
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/python/example_code/dynamodb#code-examples)\. 
+  For API details, see [UpdateItem](https://docs.aws.amazon.com/goto/boto3/dynamodb-2012-08-10/UpdateItem) in *AWS SDK for Python \(Boto3\) API Reference*\. 

------
#### [ Ruby ]

**SDK for Ruby**  
  

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
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/ruby/example_code/dynamodb#code-examples)\. 
+  For API details, see [UpdateItem](https://docs.aws.amazon.com/goto/SdkForRubyV3/dynamodb-2012-08-10/UpdateItem) in *AWS SDK for Ruby API Reference*\. 

------

For more DynamoDB examples, see [Code examples for DynamoDB using AWS SDKs](service_code_examples.md)\.