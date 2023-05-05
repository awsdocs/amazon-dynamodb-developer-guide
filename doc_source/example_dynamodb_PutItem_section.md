# Put an item in a DynamoDB table using an AWS SDK<a name="example_dynamodb_PutItem_section"></a>

The following code examples show how to put an item in a DynamoDB table\.

**Note**  
The source code for these examples is in the [AWS Code Examples GitHub repository](https://github.com/awsdocs/aws-doc-sdk-examples)\. Have feedback on a code example? [Create an Issue](https://github.com/awsdocs/aws-doc-sdk-examples/issues/new/choose) in the code examples repo\. 

------
#### [ \.NET ]

**AWS SDK for \.NET**  
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/dotnetv3/dynamodb#code-examples)\. 
  

```
        /// <summary>
        /// Adds a new item to the table.
        /// </summary>
        /// <param name="client">An initialized Amazon DynamoDB client object.</param>
        /// <param name="newMovie">A Movie object containing informtation for
        /// the movie to add to the table.</param>
        /// <param name="tableName">The name of the table where the item will be added.</param>
        /// <returns>A Boolean value that indicates the results of adding the item.</returns>
        public static async Task<bool> PutItemAsync(AmazonDynamoDBClient client, Movie newMovie, string tableName)
        {
            var item = new Dictionary<string, AttributeValue>
            {
                ["title"] = new AttributeValue { S = newMovie.Title },
                ["year"] = new AttributeValue { N = newMovie.Year.ToString() },
            };

            var request = new PutItemRequest
            {
                TableName = tableName,
                Item = item,
            };

            var response = await client.PutItemAsync(request);
            return response.HttpStatusCode == System.Net.HttpStatusCode.OK;
        }
```
+  For API details, see [PutItem](https://docs.aws.amazon.com/goto/DotNetSDKV3/dynamodb-2012-08-10/PutItem) in *AWS SDK for \.NET API Reference*\. 

------
#### [ C\+\+ ]

**SDK for C\+\+**  
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/cpp/example_code/dynamodb#code-examples)\. 
  

```
//! Put an item in an Amazon DynamoDB table.
/*!
  \sa putItem()
  \param tableName: The table name.
  \param artistKey: The artist key. This is the partition key for the table.
  \param artistValue: The artist value.
  \param albumTitleKey: The album title key.
  \param albumTitleValue: The album title value.
  \param awardsKey: The awards key.
  \param awardsValue: The awards value.
  \param songTitleKey: The song title key.
  \param songTitleValue: The song title value.
  \param clientConfiguration: AWS client configuration.
  \return bool: Function succeeded.
 */
bool AwsDoc::DynamoDB::putItem(const Aws::String &tableName,
                               const Aws::String &artistKey,
                               const Aws::String &artistValue,
                               const Aws::String &albumTitleKey,
                               const Aws::String &albumTitleValue,
                               const Aws::String &awardsKey,
                               const Aws::String &awardsValue,
                               const Aws::String &songTitleKey,
                               const Aws::String &songTitleValue,
                               const Aws::Client::ClientConfiguration &clientConfiguration) {
    Aws::DynamoDB::DynamoDBClient dynamoClient(clientConfiguration);

    Aws::DynamoDB::Model::PutItemRequest putItemRequest;
    putItemRequest.SetTableName(tableName);

    putItemRequest.AddItem(artistKey, Aws::DynamoDB::Model::AttributeValue().SetS(
            artistValue)); // This is the hash key.
    putItemRequest.AddItem(albumTitleKey, Aws::DynamoDB::Model::AttributeValue().SetS(
            albumTitleValue));
    putItemRequest.AddItem(awardsKey,
                           Aws::DynamoDB::Model::AttributeValue().SetS(awardsValue));
    putItemRequest.AddItem(songTitleKey,
                           Aws::DynamoDB::Model::AttributeValue().SetS(songTitleValue));

    const Aws::DynamoDB::Model::PutItemOutcome outcome = dynamoClient.PutItem(
            putItemRequest);
    if (outcome.IsSuccess()) {
        std::cout << "Successfully added Item!" << std::endl;
    }
    else {
        std::cerr << outcome.GetError().GetMessage() << std::endl;
    }

    return outcome.IsSuccess();
}
```
+  For API details, see [PutItem](https://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/PutItem) in *AWS SDK for C\+\+ API Reference*\. 

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



// AddMovie adds a movie the DynamoDB table.
func (basics TableBasics) AddMovie(movie Movie) error {
	item, err := attributevalue.MarshalMap(movie)
	if err != nil {
		panic(err)
	}
	_, err = basics.DynamoDbClient.PutItem(context.TODO(), &dynamodb.PutItemInput{
		TableName: aws.String(basics.TableName), Item: item,
	})
	if err != nil {
		log.Printf("Couldn't add item to table. Here's why: %v\n", err)
	}
	return err
}
```
+  For API details, see [PutItem](https://pkg.go.dev/github.com/aws/aws-sdk-go-v2/service/dynamodb#Client.PutItem) in *AWS SDK for Go API Reference*\. 

------
#### [ Java ]

**SDK for Java 2\.x**  
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javav2/example_code/dynamodb#readme)\. 
Puts an item into a table using [DynamoDbClient](http://docs.aws.amazon.com/sdk-for-java/latest/reference/software/amazon/awssdk/services/dynamodb/DynamoDbClient.html)\.  

```
    public static void putItemInTable(DynamoDbClient ddb,
                                      String tableName,
                                      String key,
                                      String keyVal,
                                      String albumTitle,
                                      String albumTitleValue,
                                      String awards,
                                      String awardVal,
                                      String songTitle,
                                      String songTitleVal){

        HashMap<String,AttributeValue> itemValues = new HashMap<>();
        itemValues.put(key, AttributeValue.builder().s(keyVal).build());
        itemValues.put(songTitle, AttributeValue.builder().s(songTitleVal).build());
        itemValues.put(albumTitle, AttributeValue.builder().s(albumTitleValue).build());
        itemValues.put(awards, AttributeValue.builder().s(awardVal).build());

        PutItemRequest request = PutItemRequest.builder()
            .tableName(tableName)
            .item(itemValues)
            .build();

        try {
            PutItemResponse response = ddb.putItem(request);
            System.out.println(tableName +" was successfully updated. The request id is "+response.responseMetadata().requestId());

        } catch (ResourceNotFoundException e) {
            System.err.format("Error: The Amazon DynamoDB table \"%s\" can't be found.\n", tableName);
            System.err.println("Be sure that it exists and that you've typed its name correctly!");
            System.exit(1);
        } catch (DynamoDbException e) {
            System.err.println(e.getMessage());
            System.exit(1);
        }
    }
```
+  For API details, see [PutItem](https://docs.aws.amazon.com/goto/SdkForJavaV2/dynamodb-2012-08-10/PutItem) in *AWS SDK for Java 2\.x API Reference*\. 

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
Put an item in a table using the DynamoDB document client\.  

```
import { PutCommand } from "@aws-sdk/lib-dynamodb";
import { ddbDocClient } from "../libs/ddbDocClient.js";

export const putItem = async () => {
  // Set the parameters.
  export const params = {
    TableName: "TABLE_NAME",
    Item: {
      primaryKey: "VALUE_1",
      sortKey: "VALUE_2",
    },
  };
  try {
    const data = await ddbDocClient.send(new PutCommand(params));
    console.log("Success - item added or updated", data);
  } catch (err) {
    console.log("Error", err.stack);
  }
};
putItem();
```
Put an item in a table using PartiQL\.  

```
// Import required AWS SDK clients and commands for Node.js.
import { ExecuteStatementCommand } from "@aws-sdk/client-dynamodb";
import { ddbDocClient } from "../libs/ddbDocClient.js";

const tableName = process.argv[2];
const movieYear1 = process.argv[3];
const movieTitle1 = process.argv[4];

export const run = async (tableName, movieTitle1, movieYear1) => {
  const params = {
    Statement: "INSERT INTO " + tableName + "  value  {'title':?, 'year':?}",
    Parameters: [{ S: movieTitle1 }, { N: movieYear1 }],
  };
  try {
    await ddbDocClient.send(new ExecuteStatementCommand(params));
    console.log("Success. Item added.");
    return "Run successfully"; // For unit tests.
  } catch (err) {
    console.error(err);
  }
};
run(tableName, movieTitle1, movieYear1);
```
Put items by batch in a table using PartiQL\.  

```
// Import required AWS SDK clients and commands for Node.js.
import { BatchExecuteStatementCommand } from "@aws-sdk/client-dynamodb";
import { ddbDocClient } from "../libs/ddbDocClient.js";

const tableName = process.argv[2];
const movieYear1 = process.argv[3];
const movieTitle1 = process.argv[4];
const movieYear2 = process.argv[5];
const movieTitle2 = process.argv[6];

export const run = async (
  tableName,
  movieYear1,
  movieTitle1,
  movieYear2,
  movieTitle2
) => {
  const params = {
    Statements: [
      {
        Statement:
          "INSERT INTO " + tableName + "  value  {'title':?, 'year':?}",
        Parameters: [{ S: movieTitle1 }, { N: movieYear1 }],
      },
      {
        Statement:
          "INSERT INTO " + tableName + "  value  {'title':?, 'year':?}",
        Parameters: [{ S: movieTitle2 }, { N: movieYear2 }],
      },
    ],
  };
  try {
    await ddbDocClient.send(
      new BatchExecuteStatementCommand(params)
    );
    console.log("Success. Items added.");
    return "Run successfully"; // For unit tests.
  } catch (err) {
    console.error(err);
  }
};
run(tableName, movieYear1, movieTitle1, movieYear2, movieTitle2);
```
+  For API details, see [PutItem](https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/clients/client-dynamodb/classes/putitemcommand.html) in *AWS SDK for JavaScript API Reference*\. 

**SDK for JavaScript V2**  
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javascript/example_code/dynamodb#code-examples)\. 
Put an item in a table\.  

```
// Load the AWS SDK for Node.js
var AWS = require('aws-sdk');
// Set the region 
AWS.config.update({region: 'REGION'});

// Create the DynamoDB service object
var ddb = new AWS.DynamoDB({apiVersion: '2012-08-10'});

var params = {
  TableName: 'CUSTOMER_LIST',
  Item: {
    'CUSTOMER_ID' : {N: '001'},
    'CUSTOMER_NAME' : {S: 'Richard Roe'}
  }
};

// Call DynamoDB to add the item to the table
ddb.putItem(params, function(err, data) {
  if (err) {
    console.log("Error", err);
  } else {
    console.log("Success", data);
  }
});
```
Put an item in a table using the DynamoDB document client\.  

```
// Load the AWS SDK for Node.js
var AWS = require('aws-sdk');
// Set the region 
AWS.config.update({region: 'REGION'});

// Create DynamoDB document client
var docClient = new AWS.DynamoDB.DocumentClient({apiVersion: '2012-08-10'});

var params = {
  TableName: 'TABLE',
  Item: {
    'HASHKEY': VALUE,
    'ATTRIBUTE_1': 'STRING_VALUE',
    'ATTRIBUTE_2': VALUE_2
  }
};

docClient.put(params, function(err, data) {
  if (err) {
    console.log("Error", err);
  } else {
    console.log("Success", data);
  }
});
```
+  For more information, see [AWS SDK for JavaScript Developer Guide](https://docs.aws.amazon.com/sdk-for-javascript/v2/developer-guide/dynamodb-example-table-read-write.html#dynamodb-example-table-read-write-writing-an-item)\. 
+  For API details, see [PutItem](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/dynamodb-2012-08-10/PutItem) in *AWS SDK for JavaScript API Reference*\. 

------
#### [ Kotlin ]

**SDK for Kotlin**  
This is prerelease documentation for a feature in preview release\. It is subject to change\.
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/kotlin/services/dynamodb#code-examples)\. 
  

```
suspend fun putItemInTable(
    tableNameVal: String,
    key: String,
    keyVal: String,
    albumTitle: String,
    albumTitleValue: String,
    awards: String,
    awardVal: String,
    songTitle: String,
    songTitleVal: String
) {
    val itemValues = mutableMapOf<String, AttributeValue>()

    // Add all content to the table.
    itemValues[key] = AttributeValue.S(keyVal)
    itemValues[songTitle] = AttributeValue.S(songTitleVal)
    itemValues[albumTitle] = AttributeValue.S(albumTitleValue)
    itemValues[awards] = AttributeValue.S(awardVal)

    val request = PutItemRequest {
        tableName = tableNameVal
        item = itemValues
    }

    DynamoDbClient { region = "us-east-1" }.use { ddb ->
        ddb.putItem(request)
        println(" A new item was placed into $tableNameVal.")
    }
}
```
+  For API details, see [PutItem](https://github.com/awslabs/aws-sdk-kotlin#generating-api-documentation) in *AWS SDK for Kotlin API reference*\. 

------
#### [ PHP ]

**SDK for PHP**  
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/php/example_code/dynamodb#code-examples)\. 
  

```
        echo "What's the name of the last movie you watched?\n";
        while (empty($movieName)) {
            $movieName = testable_readline("Movie name: ");
        }
        echo "And what year was it released?\n";
        $movieYear = "year";
        while (!is_numeric($movieYear) || intval($movieYear) != $movieYear) {
            $movieYear = testable_readline("Year released: ");
        }

        $service->putItem([
            'Item' => [
                'year' => [
                    'N' => "$movieYear",
                ],
                'title' => [
                    'S' => $movieName,
                ],
            ],
            'TableName' => $tableName,
        ]);

    public function putItem(array $array)
    {
        $this->dynamoDbClient->putItem($array);
    }
```
+  For API details, see [PutItem](https://docs.aws.amazon.com/goto/SdkForPHPV3/dynamodb-2012-08-10/PutItem) in *AWS SDK for PHP API Reference*\. 

------
#### [ Python ]

**SDK for Python \(Boto3\)**  
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/python/example_code/dynamodb#code-examples)\. 
  

```
class Movies:
    """Encapsulates an Amazon DynamoDB table of movie data."""
    def __init__(self, dyn_resource):
        """
        :param dyn_resource: A Boto3 DynamoDB resource.
        """
        self.dyn_resource = dyn_resource
        self.table = None

    def add_movie(self, title, year, plot, rating):
        """
        Adds a movie to the table.

        :param title: The title of the movie.
        :param year: The release year of the movie.
        :param plot: The plot summary of the movie.
        :param rating: The quality rating of the movie.
        """
        try:
            self.table.put_item(
                Item={
                    'year': year,
                    'title': title,
                    'info': {'plot': plot, 'rating': Decimal(str(rating))}})
        except ClientError as err:
            logger.error(
                "Couldn't add movie %s to table %s. Here's why: %s: %s",
                title, self.table.name,
                err.response['Error']['Code'], err.response['Error']['Message'])
            raise
```
+  For API details, see [PutItem](https://docs.aws.amazon.com/goto/boto3/dynamodb-2012-08-10/PutItem) in *AWS SDK for Python \(Boto3\) API Reference*\. 

------
#### [ Ruby ]

**SDK for Ruby**  
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/ruby/example_code/dynamodb#code-examples)\. 
  

```
  # Adds a movie to the table.
  #
  # @param title [String] The title of the movie.
  # @param year [Integer] The release year of the movie.
  # @param plot [String] The plot summary of the movie.
  # @param rating [Float] The quality rating of the movie.
  def add_movie(title:, year:, plot:, rating:)
    @table.put_item(
      item: {
        "year" => year,
        "title" => title,
        "info" => {"plot" => plot, "rating" => rating}})
  rescue Aws::Errors::ServiceError => e
    puts("Couldn't add movie #{title} to table #{@table.name}. Here's why:")
    puts("\t#{e.code}: #{e.message}")
    raise
  end
```
+  For API details, see [PutItem](https://docs.aws.amazon.com/goto/SdkForRubyV3/dynamodb-2012-08-10/PutItem) in *AWS SDK for Ruby API Reference*\. 

------
#### [ Rust ]

**SDK for Rust**  
This documentation is for an SDK in preview release\. The SDK is subject to change and should not be used in production\.
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/rust_dev_preview/dynamodb#code-examples)\. 
  

```
pub async fn add_item(client: &Client, item: Item, table: &String) -> Result<ItemOut, Error> {
    let user_av = AttributeValue::S(item.username);
    let type_av = AttributeValue::S(item.p_type);
    let age_av = AttributeValue::S(item.age);
    let first_av = AttributeValue::S(item.first);
    let last_av = AttributeValue::S(item.last);

    let request = client
        .put_item()
        .table_name(table)
        .item("username", user_av)
        .item("account_type", type_av)
        .item("age", age_av)
        .item("first_name", first_av)
        .item("last_name", last_av);

    println!("Executing request [{request:?}] to add item...");

    let resp = request.send().await?;

    let attributes = resp.attributes().unwrap();

    let username = attributes.get("username").cloned();
    let first_name = attributes.get("first_name").cloned();
    let last_name = attributes.get("last_name").cloned();
    let age = attributes.get("age").cloned();
    let p_type = attributes.get("p_type").cloned();

    println!(
        "Added user {:?}, {:?} {:?}, age {:?} as {:?} user",
        username, first_name, last_name, age, p_type
    );

    Ok(ItemOut {
        p_type,
        age,
        username,
        first_name,
        last_name,
    })
}
```
+  For API details, see [PutItem](https://docs.rs/releases/search?query=aws-sdk) in *AWS SDK for Rust API reference*\. 

------

For a complete list of AWS SDK developer guides and code examples, see [Using DynamoDB with an AWS SDK](sdk-general-information-section.md)\. This topic also includes information about getting started and details about previous SDK versions\.