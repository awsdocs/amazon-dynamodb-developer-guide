# Write an item to a DynamoDB table<a name="GettingStarted.WriteItem"></a>

You can write items to DynamoDB tables using the AWS Management Console, the AWS CLI, or an AWS SDK\. For more information on items, see [Core Components of Amazon DynamoDB](HowItWorks.CoreComponents.md)\.

## Write an item to a DynamoDB table using an AWS SDK<a name="GettingStarted.WriteItem.SDK"></a>

The following code examples show how to write an item to a DynamoDB table using an AWS SDK\.

------
#### [ C\+\+ ]

**SDK for C\+\+**  
  

```
        Aws::Client::ClientConfiguration clientConfig;
        Aws::DynamoDB::DynamoDBClient dynamoClient(clientConfig);

        Aws::DynamoDB::Model::PutItemRequest putItemRequest;
        putItemRequest.SetTableName(table);
               
        Aws::DynamoDB::Model::AttributeValue av;
        av.SetS(keyVal);
        
        Aws::DynamoDB::Model::AttributeValue album;
        album.SetS(AlbumTitleValue);

        Aws::DynamoDB::Model::AttributeValue awards;
        awards.SetS(AwardVal);

        Aws::DynamoDB::Model::AttributeValue song;
        song.SetS(SongTitleVal);

        // Add all AttributeValue objects.
        putItemRequest.AddItem(key, av);
        putItemRequest.AddItem(albumTitle, album);
        putItemRequest.AddItem(Awards, awards);
        putItemRequest.AddItem(SongTitle, song);

        const Aws::DynamoDB::Model::PutItemOutcome result = dynamoClient.PutItem(putItemRequest);
        if (!result.IsSuccess())
        {
            std::cout << result.GetError().GetMessage() << std::endl;
            return 1;
        }
        std::cout << "Successfully added Item!" << std::endl;
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/cpp/example_code/dynamodb#code-examples)\. 
+  For API details, see [PutItem](https://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/PutItem) in *AWS SDK for C\+\+ API Reference*\. 

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
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/gov2/dynamodb#code-examples)\. 
+  For API details, see [PutItem](https://pkg.go.dev/github.com/aws/aws-sdk-go-v2/service/dynamodb#Client.PutItem) in *AWS SDK for Go API Reference*\. 

------
#### [ Java ]

**SDK for Java 2\.x**  
Puts an item into a table by using the enhanced client\.  

```
    public static void putRecord(DynamoDbEnhancedClient enhancedClient) {

        try {
            DynamoDbTable<Customer> custTable = enhancedClient.table("Customer", TableSchema.fromBean(Customer.class));

            // Create an Instant
            LocalDate localDate = LocalDate.parse("2020-04-07");
            LocalDateTime localDateTime = localDate.atStartOfDay();
            Instant instant = localDateTime.toInstant(ZoneOffset.UTC);

            // Populate the Table
            Customer custRecord = new Customer();
            custRecord.setCustName("Susan red");
            custRecord.setId("id146");
            custRecord.setEmail("sred@noserver.com");
            custRecord.setRegistrationDate(instant) ;

            // Put the customer data into a DynamoDB table
            custTable.putItem(custRecord);

        } catch (DynamoDbException e) {
            System.err.println(e.getMessage());
            System.exit(1);
        }
        System.out.println("done");
    }
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javav2/example_code/dynamodb#readme)\. 
+  For API details, see [PutItem](https://docs.aws.amazon.com/goto/SdkForJavaV2/dynamodb-2012-08-10/PutItem) in *AWS SDK for Java 2\.x API Reference*\. 

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
    const data = await ddbDocClient.send(new ExecuteStatementCommand(params));
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
    const data = await ddbDocClient.send(
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
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javascriptv3/example_code/dynamodb#code-examples)\. 
+  For API details, see [PutItem](https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/clients/client-dynamodb/classes/putitemcommand.html) in *AWS SDK for JavaScript API Reference*\. 

**SDK for JavaScript V2**  
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
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javascript/example_code/dynamodb#code-examples)\. 
+  For more information, see [AWS SDK for JavaScript Developer Guide](https://docs.aws.amazon.com/sdk-for-javascript/v2/developer-guide/dynamodb-example-table-read-write.html#dynamodb-example-table-read-write-writing-an-item)\. 
+  For API details, see [PutItem](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/dynamodb-2012-08-10/PutItem) in *AWS SDK for JavaScript API Reference*\. 

------
#### [ Kotlin ]

**SDK for Kotlin**  
This is prerelease documentation for a feature in preview release\. It is subject to change\.
  

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
        itemValues[albumTitle] =  AttributeValue.S(albumTitleValue)
        itemValues[awards] = AttributeValue.S(awardVal)

        val request = PutItemRequest {
            tableName=tableNameVal
            item = itemValues
        }

       DynamoDbClient { region = "us-east-1" }.use { ddb ->
            ddb.putItem(request)
            println(" A new item was placed into $tableNameVal.")
        }
 }
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/kotlin/services/dynamodb#code-examples)\. 
+  For API details, see [PutItem](https://github.com/awslabs/aws-sdk-kotlin#generating-api-documentation) in *AWS SDK for Kotlin API reference*\. 

------
#### [ Python ]

**SDK for Python \(Boto3\)**  
  

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
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/python/example_code/dynamodb#code-examples)\. 
+  For API details, see [PutItem](https://docs.aws.amazon.com/goto/boto3/dynamodb-2012-08-10/PutItem) in *AWS SDK for Python \(Boto3\) API Reference*\. 

------
#### [ Ruby ]

**SDK for Ruby**  
  

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
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/ruby/example_code/dynamodb#code-examples)\. 
+  For API details, see [PutItem](https://docs.aws.amazon.com/goto/SdkForRubyV3/dynamodb-2012-08-10/PutItem) in *AWS SDK for Ruby API Reference*\. 

------
#### [ Rust ]

**SDK for Rust**  
This documentation is for an SDK in preview release\. The SDK is subject to change and should not be used in production\.
  

```
async fn add_item(
    client: &Client,
    table: &str,
    username: &str,
    p_type: &str,
    age: &str,
    first: &str,
    last: &str,
) -> Result<(), Error> {
    let user_av = AttributeValue::S(username.into());
    let type_av = AttributeValue::S(p_type.into());
    let age_av = AttributeValue::S(age.into());
    let first_av = AttributeValue::S(first.into());
    let last_av = AttributeValue::S(last.into());

    let request = client
        .put_item()
        .table_name(table)
        .item("username", user_av)
        .item("account_type", type_av)
        .item("age", age_av)
        .item("first_name", first_av)
        .item("last_name", last_av);

    println!("Executing request [{:?}] to add item...", request);

    request.send().await?;

    println!(
        "Added user {}, {} {}, age {} as {} user",
        username, first, last, age, p_type
    );

    Ok(())
}
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/rust_dev_preview/dynamodb#code-examples)\. 
+  For API details, see [PutItem](https://docs.rs/releases/search?query=aws-sdk) in *AWS SDK for Rust API reference*\. 

------

For more DynamoDB examples, see [Code examples for DynamoDB using AWS SDKs](service_code_examples.md)\.