# Write a batch of DynamoDB items using an AWS SDK<a name="example_dynamodb_BatchWriteItem_section"></a>

The following code examples show how to write a batch of DynamoDB items\.

**Note**  
The source code for these examples is in the [AWS Code Examples GitHub repository](https://github.com/awsdocs/aws-doc-sdk-examples)\. Have feedback on a code example? [Create an Issue](https://github.com/awsdocs/aws-doc-sdk-examples/issues/new/choose) in the code examples repo\. 

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
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/gov2/dynamodb#code-examples)\. 
+  For API details, see [BatchWriteItem](https://pkg.go.dev/github.com/aws/aws-sdk-go-v2/service/dynamodb#Client.BatchWriteItem) in *AWS SDK for Go API Reference*\. 

------
#### [ Java ]

**SDK for Java 2\.x**  
Inserts many items into a table by using the enhanced client\.  

```
    public static void putBatchRecords(DynamoDbEnhancedClient enhancedClient) {
        try {

           DynamoDbTable<Customer> mappedTable = enhancedClient.table("Customer", TableSchema.fromBean(Customer.class));

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

            // Create a BatchWriteItemEnhancedRequest object
            BatchWriteItemEnhancedRequest batchWriteItemEnhancedRequest =
                    BatchWriteItemEnhancedRequest.builder()
                            .writeBatches(
                                    WriteBatch.builder(Customer.class)
                                            .mappedTableResource(mappedTable)
                                            .addPutItem(r -> r.item(record2))
                                            .addPutItem(r -> r.item(record3))
                                            .build())
                            .build();

            // Add these two items to the table
            enhancedClient.batchWriteItem(batchWriteItemEnhancedRequest);
            System.out.println("done");

        } catch (DynamoDbException e) {
            System.err.println(e.getMessage());
            System.exit(1);
        }
    }
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javav2/example_code/dynamodb/#readme)\. 
+  For API details, see [BatchWriteItem](https://docs.aws.amazon.com/goto/SdkForJavaV2/dynamodb-2012-08-10/BatchWriteItem) in *AWS SDK for Java 2\.x API Reference*\. 

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
Create the document client\.  

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
        const data = ddbDocClient.send(new BatchWriteCommand(params));
      }
      console.log("Success, table updated.");
    }
  } catch (error) {
    console.log("Error", error);
  }
};
writeData();
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javascriptv3/example_code/dynamodb#code-examples)\. 
+  For API details, see [BatchWriteItem](https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/clients/client-dynamodb/classes/batchwriteitemcommand.html) in *AWS SDK for JavaScript API Reference*\. 

**SDK for JavaScript V2**  
  

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
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javascript/example_code/dynamodb#code-examples)\. 
+  For more information, see [AWS SDK for JavaScript Developer Guide](https://docs.aws.amazon.com/sdk-for-javascript/v2/developer-guide/dynamodb-example-table-read-write-batch.html#dynamodb-example-table-read-write-batch-writing)\. 
+  For API details, see [BatchWriteItem](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/dynamodb-2012-08-10/BatchWriteItem) in *AWS SDK for JavaScript API Reference*\. 

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
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/python/example_code/dynamodb#code-examples)\. 
+  For API details, see [BatchWriteItem](https://docs.aws.amazon.com/goto/boto3/dynamodb-2012-08-10/BatchWriteItem) in *AWS SDK for Python \(Boto3\) API Reference*\. 

------
#### [ Ruby ]

**SDK for Ruby**  
  

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
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/ruby/example_code/dynamodb#code-examples)\. 
+  For API details, see [BatchWriteItem](https://docs.aws.amazon.com/goto/SdkForRubyV3/dynamodb-2012-08-10/BatchWriteItem) in *AWS SDK for Ruby API Reference*\. 

------

For a complete list of AWS SDK developer guides and code examples, see [Using DynamoDB with an AWS SDK](sdk-general-information-section.md)\. This topic also includes information about getting started and details about previous SDK versions\.