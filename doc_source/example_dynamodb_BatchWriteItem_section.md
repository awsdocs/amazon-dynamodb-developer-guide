# Write a batch of DynamoDB items using an AWS SDK<a name="example_dynamodb_BatchWriteItem_section"></a>

The following code examples show how to write a batch of DynamoDB items\.

**Note**  
The source code for these examples is in the [AWS Code Examples GitHub repository](https://github.com/awsdocs/aws-doc-sdk-examples)\. Have feedback on a code example? [Create an Issue](https://github.com/awsdocs/aws-doc-sdk-examples/issues/new/choose) in the code examples repo\. 

------
#### [ \.NET ]

**AWS SDK for \.NET**  
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/dotnetv3/dynamodb#code-examples)\. 
Writes a batch of items to the movie table\.  

```
        /// <summary>
        /// Loads the contents of a JSON file into a list of movies to be
        /// added to the DynamoDB table.
        /// </summary>
        /// <param name="movieFileName">The full path to the JSON file.</param>
        /// <returns>A generic list of movie objects.</returns>
        public static List<Movie> ImportMovies(string movieFileName)
        {
            if (!File.Exists(movieFileName))
            {
                return null;
            }

            using var sr = new StreamReader(movieFileName);
            string json = sr.ReadToEnd();
            var allMovies = JsonConvert.DeserializeObject<List<Movie>>(json);

            // Now return the first 250 entries.
            return allMovies.GetRange(0, 250);
        }

        /// <summary>
        /// Writes 250 items to the movie table.
        /// </summary>
        /// <param name="client">The initialized DynamoDB client object.</param>
        /// <param name="movieFileName">A string containing the full path to
        /// the JSON file containing movie data.</param>
        /// <returns>A long integer value representing the number of movies
        /// imported from the JSON file.</returns>
        public static async Task<long> BatchWriteItemsAsync(
            AmazonDynamoDBClient client,
            string movieFileName)
        {
            var movies = ImportMovies(movieFileName);
            if (movies is null)
            {
                Console.WriteLine("Couldn't find the JSON file with movie data.");
                return 0;
            }

            var context = new DynamoDBContext(client);

            var bookBatch = context.CreateBatchWrite<Movie>();
            bookBatch.AddPutItems(movies);

            Console.WriteLine("Adding imported movies to the table.");
            await bookBatch.ExecuteAsync();

            return movies.Count;
        }
```
+  For API details, see [BatchWriteItem](https://docs.aws.amazon.com/goto/DotNetSDKV3/dynamodb-2012-08-10/BatchWriteItem) in *AWS SDK for \.NET API Reference*\. 

------
#### [ C\+\+ ]

**SDK for C\+\+**  
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/cpp/example_code/dynamodb#code-examples)\. 
  

```
//! Batch write items from a JSON file.
/*!
  \sa batchWriteItem()
  \param jsonFilePath: JSON file path.
  \param clientConfiguration: AWS client configuration.
  \return bool: Function succeeded.
 */

/*
 * The input for this routine is a JSON file that you can download from the following URL:
 * https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/SampleData.html.
 *
 * The JSON data uses the BatchWriteItem API request syntax. The JSON strings are
 * converted to AttributeValue objects. These AttributeValue objects will then generate
 * JSON strings when constructing the BatchWriteItem request, essentially outputting
 * their input.
 *
 * This is perhaps an artificial example, but it demonstrates the APIs.
 */

bool AwsDoc::DynamoDB::batchWriteItem(const Aws::String &jsonFilePath,
                                      const Aws::Client::ClientConfiguration &clientConfiguration) {
    std::ifstream fileStream(jsonFilePath);

    if (!fileStream) {
        std::cerr << "Error: could not open file '" << jsonFilePath << "'."
                  << std::endl;
    }

    std::stringstream stringStream;
    stringStream << fileStream.rdbuf();
    Aws::Utils::Json::JsonValue jsonValue(stringStream);

    Aws::DynamoDB::Model::BatchWriteItemRequest batchWriteItemRequest;
    Aws::Map<Aws::String, Aws::Utils::Json::JsonView> level1Map = jsonValue.View().GetAllObjects();
    for (const auto &level1Entry: level1Map) {
        const Aws::Utils::Json::JsonView &entriesView = level1Entry.second;
        const Aws::String &tableName = level1Entry.first;
        // The JSON entries at this level are as follows:
        //  key - table name
        //  value - list of request objects
        if (!entriesView.IsListType()) {
            std::cerr << "Error: JSON file entry '"
                      << tableName << "' is not a list." << std::endl;
            continue;
        }

        Aws::Utils::Array<Aws::Utils::Json::JsonView> entries = entriesView.AsArray();

        Aws::Vector<Aws::DynamoDB::Model::WriteRequest> writeRequests;
        if (AwsDoc::DynamoDB::addWriteRequests(tableName, entries,
                                               writeRequests)) {
            batchWriteItemRequest.AddRequestItems(tableName, writeRequests);
        }
    }

    Aws::DynamoDB::DynamoDBClient dynamoClient(clientConfiguration);

    Aws::DynamoDB::Model::BatchWriteItemOutcome outcome = dynamoClient.BatchWriteItem(
            batchWriteItemRequest);

    if (outcome.IsSuccess()) {
        std::cout << "DynamoDB::BatchWriteItem was successful." << std::endl;
    }
    else {
        std::cerr << "Error with DynamoDB::BatchWriteItem. "
                  << outcome.GetError().GetMessage()
                  << std::endl;
    }

    return true;
}

//! Convert requests in JSON format to a vector of WriteRequest objects.
/*!
  \sa addWriteRequests()
  \param tableName: Name of the table for the write operations.
  \param requestsJson: Request data in JSON format.
  \param writeRequests: Vector to receive the WriteRequest objects.
  \return bool: Function succeeded.
 */
bool AwsDoc::DynamoDB::addWriteRequests(const Aws::String &tableName,
                                        const Aws::Utils::Array<Aws::Utils::Json::JsonView> &requestsJson,
                                        Aws::Vector<Aws::DynamoDB::Model::WriteRequest> &writeRequests) {
    for (size_t i = 0; i < requestsJson.GetLength(); ++i) {
        const Aws::Utils::Json::JsonView &requestsEntry = requestsJson[i];
        if (!requestsEntry.IsObject()) {
            std::cerr << "Error: incorrect requestsEntry type "
                      << requestsEntry.WriteReadable() << std::endl;
            return false;
        }

        Aws::Map<Aws::String, Aws::Utils::Json::JsonView> requestsMap = requestsEntry.GetAllObjects();

        for (const auto &request: requestsMap) {
            const Aws::String &requestType = request.first;
            const Aws::Utils::Json::JsonView &requestJsonView = request.second;

            if (requestType == "PutRequest") {
                if (!requestJsonView.ValueExists("Item")) {
                    std::cerr << "Error: item key missing for requests "
                              << requestJsonView.WriteReadable() << std::endl;
                    return false;
                }
                Aws::Map<Aws::String, Aws::DynamoDB::Model::AttributeValue> attributes;
                if (!getAttributeObjectsMap(requestJsonView.GetObject("Item"),
                                            attributes)) {
                    std::cerr << "Error getting attributes "
                              << requestJsonView.WriteReadable() << std::endl;
                    return false;
                }

                Aws::DynamoDB::Model::PutRequest putRequest;
                putRequest.SetItem(attributes);
                writeRequests.push_back(
                        Aws::DynamoDB::Model::WriteRequest().WithPutRequest(
                                putRequest));
            }
            else {
                std::cerr << "Error: unimplemented request type '" << requestType
                          << "'." << std::endl;
            }
        }
    }

    return true;
}

//! Generate a map of AttributeValue objects from JSON records.
/*!
  \sa getAttributeObjectsMap()
  \param jsonView: JSONView of attribute records.
  \param writeRequests: Map to receive the AttributeValue objects.
  \return bool: Function succeeded.
 */
bool
AwsDoc::DynamoDB::getAttributeObjectsMap(const Aws::Utils::Json::JsonView &jsonView,
                                         Aws::Map<Aws::String, Aws::DynamoDB::Model::AttributeValue> &attributes) {
    Aws::Map<Aws::String, Aws::Utils::Json::JsonView> objectsMap = jsonView.GetAllObjects();
    for (const auto &entry: objectsMap) {
        const Aws::String &attributeKey = entry.first;
        const Aws::Utils::Json::JsonView &attributeJsonView = entry.second;

        if (!attributeJsonView.IsObject()) {
            std::cerr << "Error: attribute not an object "
                      << attributeJsonView.WriteReadable() << std::endl;
            return false;
        }

        attributes.emplace(attributeKey,
                           Aws::DynamoDB::Model::AttributeValue(attributeJsonView));
    }

    return true;
}
```
+  For API details, see [BatchWriteItem](https://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/BatchWriteItem) in *AWS SDK for C\+\+ API Reference*\. 

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



// AddMovieBatch adds a slice of movies to the DynamoDB table. The function sends
// batches of 25 movies to DynamoDB until all movies are added or it reaches the
// specified maximum.
func (basics TableBasics) AddMovieBatch(movies []Movie, maxMovies int) (int, error) {
	var err error
	var item map[string]types.AttributeValue
	written := 0
	batchSize := 25 // DynamoDB allows a maximum batch size of 25 items.
	start := 0
	end := start + batchSize
	for start < maxMovies && start < len(movies) {
		var writeReqs []types.WriteRequest
		if end > len(movies) {
			end = len(movies)
		}
		for _, movie := range movies[start:end] {
			item, err = attributevalue.MarshalMap(movie)
			if err != nil {
				log.Printf("Couldn't marshal movie %v for batch writing. Here's why: %v\n", movie.Title, err)
			} else {
				writeReqs = append(
					writeReqs,
					types.WriteRequest{PutRequest: &types.PutRequest{Item: item}},
				)
			}
		}
		_, err = basics.DynamoDbClient.BatchWriteItem(context.TODO(), &dynamodb.BatchWriteItemInput{
			RequestItems: map[string][]types.WriteRequest{basics.TableName: writeReqs}})
		if err != nil {
			log.Printf("Couldn't add a batch of movies to %v. Here's why: %v\n", basics.TableName, err)
		} else {
			written += len(writeReqs)
		}
		start = end
		end += batchSize
	}

	return written, err
}
```
+  For API details, see [BatchWriteItem](https://pkg.go.dev/github.com/aws/aws-sdk-go-v2/service/dynamodb#Client.BatchWriteItem) in *AWS SDK for Go API Reference*\. 

------
#### [ Java ]

**SDK for Java 2\.x**  
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javav2/example_code/dynamodb/#readme)\. 
Inserts many items into a table by using the enhanced client\.  

```
    public static void putBatchRecords(DynamoDbEnhancedClient enhancedClient) {

        try {
            DynamoDbTable<Customer> customerMappedTable = enhancedClient.table("Customer", TableSchema.fromBean(Customer.class));
            DynamoDbTable<Music> musicMappedTable = enhancedClient.table("Music", TableSchema.fromBean(Music.class));
            LocalDate localDate = LocalDate.parse("2020-04-07");
            LocalDateTime localDateTime = localDate.atStartOfDay();
            Instant instant = localDateTime.toInstant(ZoneOffset.UTC);

            Customer record2 = new Customer();
            record2.setCustName("Fred Pink");
            record2.setId("id110");
            record2.setEmail("fredp@noserver.com");
            record2.setRegistrationDate(instant) ;

            Customer record3 = new Customer();
            record3.setCustName("Susan Pink");
            record3.setId("id120");
            record3.setEmail("spink@noserver.com");
            record3.setRegistrationDate(instant) ;

            Customer record4 = new Customer();
            record4.setCustName("Jerry orange");
            record4.setId("id101");
            record4.setEmail("jorange@noserver.com");
            record4.setRegistrationDate(instant) ;


            BatchWriteItemEnhancedRequest batchWriteItemEnhancedRequest = BatchWriteItemEnhancedRequest.builder()
                    .writeBatches(
                            WriteBatch.builder(Customer.class)          // add items to the Customer table
                                    .mappedTableResource(customerMappedTable)
                                    .addPutItem(builder -> builder.item(record2))
                                    .addPutItem(builder -> builder.item(record3))
                                    .addPutItem(builder -> builder.item(record4))
                                    .build(),
                            WriteBatch.builder(Music.class)             // delete an item from the Music table
                                    .mappedTableResource(musicMappedTable)
                                    .addDeleteItem(builder -> builder.key(
                                            Key.builder().partitionValue("Famous Band").build()))
                                    .build())
                    .build();


            // Add three items to the Customer table and delete one item from the Music table
            enhancedClient.batchWriteItem(batchWriteItemEnhancedRequest);

            System.out.println("done");

        } catch (DynamoDbException e) {
            System.err.println(e.getMessage());
            System.exit(1);
        }
    }
```
+  For API details, see [BatchWriteItem](https://docs.aws.amazon.com/goto/SdkForJavaV2/dynamodb-2012-08-10/BatchWriteItem) in *AWS SDK for Java 2\.x API Reference*\. 

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
Create the document client\.  

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
Add the items to the table\.  

```
import fs from "fs";
import * as R from "ramda";
import { ddbDocClient } from "../libs/ddbDocClient.js";
import { BatchWriteCommand } from "@aws-sdk/lib-dynamodb";

export const writeData = async () => {
  // Before you run this example, download 'movies.json' from https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GettingStarted.Js.02.html,
  // and put it in the same folder as the example.
  // Get the movie data parse to convert into a JSON object.
  const allMovies = JSON.parse(fs.readFileSync("moviedata.json", "utf8"));
  // Split the table into segments of 25.
  const dataSegments = R.splitEvery(25, allMovies);
  const TABLE_NAME = "TABLE_NAME"
  try {
    // Loop batch write operation 10 times to upload 250 items.
    for (let i = 0; i < 10; i++) {
      const segment = dataSegments[i];
      for (let j = 0; j < 25; j++) {
        const params = {
          RequestItems: {
            [TABLE_NAME]: [
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
        ddbDocClient.send(new BatchWriteCommand(params));
      }
      console.log("Success, table updated.");
    }
  } catch (error) {
    console.log("Error", error);
  }
};
writeData();
```
+  For API details, see [BatchWriteItem](https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/clients/client-dynamodb/classes/batchwriteitemcommand.html) in *AWS SDK for JavaScript API Reference*\. 

**SDK for JavaScript V2**  
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javascript/example_code/dynamodb#code-examples)\. 
  

```
// Load the AWS SDK for Node.js
var AWS = require('aws-sdk');
// Set the region
AWS.config.update({region: 'REGION'});

// Create DynamoDB service object
var ddb = new AWS.DynamoDB({apiVersion: '2012-08-10'});

var params = {
  RequestItems: {
    "TABLE_NAME": [
       {
         PutRequest: {
           Item: {
             "KEY": { "N": "KEY_VALUE" },
               "ATTRIBUTE_1": { "S": "ATTRIBUTE_1_VALUE" },
               "ATTRIBUTE_2": { "N": "ATTRIBUTE_2_VALUE" }
           }
         }
       },
       {
         PutRequest: {
           Item: {
             "KEY": { "N": "KEY_VALUE" },
               "ATTRIBUTE_1": { "S": "ATTRIBUTE_1_VALUE" },
               "ATTRIBUTE_2": { "N": "ATTRIBUTE_2_VALUE" }
           }
         }
       }
    ]
  }
};

ddb.batchWriteItem(params, function(err, data) {
  if (err) {
    console.log("Error", err);
  } else {
    console.log("Success", data);
  }
});
```
+  For more information, see [AWS SDK for JavaScript Developer Guide](https://docs.aws.amazon.com/sdk-for-javascript/v2/developer-guide/dynamodb-example-table-read-write-batch.html#dynamodb-example-table-read-write-batch-writing)\. 
+  For API details, see [BatchWriteItem](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/dynamodb-2012-08-10/BatchWriteItem) in *AWS SDK for JavaScript API Reference*\. 

------
#### [ PHP ]

**SDK for PHP**  
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/php/example_code/dynamodb#code-examples)\. 
  

```
    public function writeBatch(string $TableName, array $Batch, int $depth = 2)
    {
        if (--$depth <= 0) {
            throw new Exception("Max depth exceeded. Please try with fewer batch items or increase depth.");
        }

        $marshal = new Marshaler();
        $total = 0;
        foreach (array_chunk($Batch, 25) as $Items) {
            foreach ($Items as $Item) {
                $BatchWrite['RequestItems'][$TableName][] = ['PutRequest' => ['Item' => $marshal->marshalItem($Item)]];
            }
            try {
                echo "Batching another " . count($Items) . " for a total of " . ($total += count($Items)) . " items!\n";
                $response = $this->dynamoDbClient->batchWriteItem($BatchWrite);
                $BatchWrite = [];
            } catch (Exception $e) {
                echo "uh oh...";
                echo $e->getMessage();
                die();
            }
            if ($total >= 250) {
                echo "250 movies is probably enough. Right? We can stop there.\n";
                break;
            }
        }
    }
```
+  For API details, see [BatchWriteItem](https://docs.aws.amazon.com/goto/SdkForPHPV3/dynamodb-2012-08-10/BatchWriteItem) in *AWS SDK for PHP API Reference*\. 

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

    def write_batch(self, movies):
        """
        Fills an Amazon DynamoDB table with the specified data, using the Boto3
        Table.batch_writer() function to put the items in the table.
        Inside the context manager, Table.batch_writer builds a list of
        requests. On exiting the context manager, Table.batch_writer starts sending
        batches of write requests to Amazon DynamoDB and automatically
        handles chunking, buffering, and retrying.

        :param movies: The data to put in the table. Each item must contain at least
                       the keys required by the schema that was specified when the
                       table was created.
        """
        try:
            with self.table.batch_writer() as writer:
                for movie in movies:
                    writer.put_item(Item=movie)
        except ClientError as err:
            logger.error(
                "Couldn't load data into table %s. Here's why: %s: %s", self.table.name,
                err.response['Error']['Code'], err.response['Error']['Message'])
            raise
```
+  For API details, see [BatchWriteItem](https://docs.aws.amazon.com/goto/boto3/dynamodb-2012-08-10/BatchWriteItem) in *AWS SDK for Python \(Boto3\) API Reference*\. 

------
#### [ Ruby ]

**SDK for Ruby**  
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/ruby/example_code/dynamodb#code-examples)\. 
  

```
  # Fills an Amazon DynamoDB table with the specified data. Items are sent in
  # batches of 25 until all items are written.
  #
  # @param movies [Enumerable] The data to put in the table. Each item must contain at least
  #                            the keys required by the schema that was specified when the
  #                            table was created.
  def write_batch(movies)
    index = 0
    slice_size = 25
    while index < movies.length
      movie_items = []
      movies[index, slice_size].each do |movie|
        movie_items.append({put_request: { item: movie }})
      end
      @dynamo_resource.batch_write_item({request_items: { @table.name => movie_items }})
      index += slice_size
    end
  rescue Aws::Errors::ServiceError => e
    puts(
      "Couldn't load data into table #{@table.name}. Here's why:")
      puts("\t#{e.code}: #{e.message}")
    raise
  end
```
+  For API details, see [BatchWriteItem](https://docs.aws.amazon.com/goto/SdkForRubyV3/dynamodb-2012-08-10/BatchWriteItem) in *AWS SDK for Ruby API Reference*\. 

------

For a complete list of AWS SDK developer guides and code examples, see [Using DynamoDB with an AWS SDK](sdk-general-information-section.md)\. This topic also includes information about getting started and details about previous SDK versions\.