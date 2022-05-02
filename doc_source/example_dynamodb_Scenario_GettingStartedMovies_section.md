# Get started using DynamoDB tables, items, and queries using an AWS SDK<a name="example_dynamodb_Scenario_GettingStartedMovies_section"></a>

The following code examples show how to:
+ Create a table that can hold movie data\.
+ Put, get, and update a single movie in the table\.
+ Write movie data to the table from a sample JSON file\.
+ Query for movies that were released in a given year\.
+ Scan for movies that were released in a range of years\.
+ Delete a movie from the table\.
+ Delete the table\.

**Note**  
The source code for these examples is in the [AWS Code Examples GitHub repository](https://github.com/awsdocs/aws-doc-sdk-examples)\. Have feedback on a code example? [Create an Issue](https://github.com/awsdocs/aws-doc-sdk-examples/issues/new/choose) in the code examples repo\. 

------
#### [ Go ]

**SDK for Go V2**  
Create a struct and methods that call DynamoDB actions\.  

```
// TableBasics encapsulates the Amazon DynamoDB service actions used in the examples.
// It contains a DynamoDB service client that is used to act on the specified table.
type TableBasics struct {
	DynamoDbClient *dynamodb.Client
	TableName      string
}



// TableExists determines whether a DynamoDB table exists.
func (basics TableBasics) TableExists() (bool, error) {
	exists := true
	_, err := basics.DynamoDbClient.DescribeTable(
		context.TODO(), &dynamodb.DescribeTableInput{TableName: aws.String(basics.TableName)},
	)
	if err != nil {
		var notFoundEx *types.ResourceNotFoundException
		if errors.As(err, &notFoundEx) {
			log.Printf("Table %v does not exist.\n", basics.TableName)
			err = nil
		} else {
			log.Printf("Couldn't determine existence of table %v. Here's why: %v\n", basics.TableName, err)
		}
		exists = false
	}
	return exists, err
}



// CreateMovieTable creates a DynamoDB table with a composite primary key defined as
// a string sort key named `title`, and a numeric partition key named `year`.
// This function uses NewTableExistsWaiter to wait for the table to be created by
// DynamoDB before it returns.
func (basics TableBasics) CreateMovieTable() (*types.TableDescription, error) {
	var tableDesc *types.TableDescription
	table, err := basics.DynamoDbClient.CreateTable(context.TODO(), &dynamodb.CreateTableInput{
		AttributeDefinitions: []types.AttributeDefinition{{
			AttributeName: aws.String("year"),
			AttributeType: types.ScalarAttributeTypeN,
		}, {
			AttributeName: aws.String("title"),
			AttributeType: types.ScalarAttributeTypeS,
		}},
		KeySchema: []types.KeySchemaElement{{
			AttributeName: aws.String("year"),
			KeyType:       types.KeyTypeHash,
		}, {
			AttributeName: aws.String("title"),
			KeyType:       types.KeyTypeRange,
		}},
		TableName: aws.String(basics.TableName),
		ProvisionedThroughput: &types.ProvisionedThroughput{
			ReadCapacityUnits:  aws.Int64(10),
			WriteCapacityUnits: aws.Int64(10),
		},
	})
	if err != nil {
		log.Printf("Couldn't create table %v. Here's why: %v\n", basics.TableName, err)
	} else {
		waiter := dynamodb.NewTableExistsWaiter(basics.DynamoDbClient)
		err = waiter.Wait(context.TODO(), &dynamodb.DescribeTableInput{
			TableName: aws.String(basics.TableName)}, 5*time.Minute)
		if err != nil {
			log.Printf("Wait for table exists failed. Here's why: %v\n", err)
		}
		tableDesc = table.TableDescription
	}
	return tableDesc, err
}



// ListTables lists the DynamoDB table names for the current account.
func (basics TableBasics) ListTables() ([]string, error) {
	var tableNames []string
	tables, err := basics.DynamoDbClient.ListTables(
		context.TODO(), &dynamodb.ListTablesInput{})
	if err != nil {
		log.Printf("Couldn't list tables. Here's why: %v\n", err)
	} else {
		tableNames = tables.TableNames
	}
	return tableNames, err
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



// GetMovie gets movie data from the DynamoDB table by using the primary composite key
// made of title and year.
func (basics TableBasics) GetMovie(title string, year int) (Movie, error) {
	movie := Movie{Title: title, Year: year}
	response, err := basics.DynamoDbClient.GetItem(context.TODO(), &dynamodb.GetItemInput{
		Key: movie.GetKey(), TableName: aws.String(basics.TableName),
	})
	if err != nil {
		log.Printf("Couldn't get info about %v. Here's why: %v\n", title, err)
	} else {
		err = attributevalue.UnmarshalMap(response.Item, &movie)
		if err != nil {
			log.Printf("Couldn't unmarshal response. Here's why: %v\n", err)
		}
	}
	return movie, err
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



// Scan gets all movies in the DynamoDB table that were released in a range of years
// and projects them to return a reduced set of fields.
// The function uses the `expression` package to build the filter and projection
// expressions.
func (basics TableBasics) Scan(startYear int, endYear int) ([]Movie, error) {
	var movies []Movie
	var err error
	var response *dynamodb.ScanOutput
	filtEx := expression.Name("year").Between(expression.Value(startYear), expression.Value(endYear))
	projEx := expression.NamesList(
		expression.Name("year"), expression.Name("title"), expression.Name("info.rating"))
	expr, err := expression.NewBuilder().WithFilter(filtEx).WithProjection(projEx).Build()
	if err != nil {
		log.Printf("Couldn't build expressions for scan. Here's why: %v\n", err)
	} else {
		response, err = basics.DynamoDbClient.Scan(context.TODO(), &dynamodb.ScanInput{
			TableName:                 aws.String(basics.TableName),
			ExpressionAttributeNames:  expr.Names(),
			ExpressionAttributeValues: expr.Values(),
			FilterExpression:          expr.Filter(),
			ProjectionExpression:      expr.Projection(),
		})
		if err != nil {
			log.Printf("Couldn't scan for movies released between %v and %v. Here's why: %v\n",
				startYear, endYear, err)
		} else {
			err = attributevalue.UnmarshalListOfMaps(response.Items, &movies)
			if err != nil {
				log.Printf("Couldn't unmarshal query response. Here's why: %v\n", err)
			}
		}
	}
	return movies, err
}



// DeleteMovie removes a movie from the DynamoDB table.
func (basics TableBasics) DeleteMovie(movie Movie) error {
	_, err := basics.DynamoDbClient.DeleteItem(context.TODO(), &dynamodb.DeleteItemInput{
		TableName: aws.String(basics.TableName), Key: movie.GetKey(),
	})
	if err != nil {
		log.Printf("Couldn't delete %v from the table. Here's why: %v\n", movie.Title, err)
	}
	return err
}



// DeleteTable deletes the DynamoDB table and all of its data.
func (basics TableBasics) DeleteTable() error {
	_, err := basics.DynamoDbClient.DeleteTable(context.TODO(), &dynamodb.DeleteTableInput{
		TableName: aws.String(basics.TableName)})
	if err != nil {
		log.Printf("Couldn't delete table %v. Here's why: %v\n", basics.TableName, err)
	}
	return err
}
```
Run an interactive scenario to create the table and perform actions on it\.  

```
// RunScenario is an interactive example that shows you how to use the AWS SDK for Go
// to create and use an Amazon DynamoDB table that stores data about movies.
//
//   1. Create a table that can hold movie data.
//   2. Put, get, and update a single movie in the table.
//   3. Write movie data to the table from a sample JSON file.
//   4. Query for movies that were released in a given year.
//   5. Scan for movies that were released in a range of years.
//   6. Delete a movie from the table.
//   7. Delete the table.
//
// This example creates a DynamoDB service client from the specified sdkConfig so that
// you can replace it with a mocked or stubbed config for unit testing.
//
// It uses a questioner from the `demotools` package to get input during the example.
// This package can be found in the ..\..\demotools folder of this repo.
//
// The specified movie sampler is used to get sample data from a URL that is loaded
// into the named table.
func RunScenario(
	sdkConfig aws.Config, questioner demotools.IQuestioner, tableName string,
	movieSampler actions.IMovieSampler) {
	defer func() {
		if r := recover(); r != nil {
			fmt.Printf("Something went wrong with the demo.")
		}
	}()

	log.Println(strings.Repeat("-", 88))
	log.Println("Welcome to the Amazon DynamoDB getting started demo.")
	log.Println(strings.Repeat("-", 88))

	tableBasics := actions.TableBasics{TableName: tableName,
		DynamoDbClient: dynamodb.NewFromConfig(sdkConfig)}

	exists, err := tableBasics.TableExists()
	if err != nil {
		panic(err)
	}
	if !exists {
		log.Printf("Creating table %v...\n", tableName)
		_, err = tableBasics.CreateMovieTable()
		if err != nil {
			panic(err)
		} else {
			log.Printf("Created table %v.\n", tableName)
		}
	} else {
		log.Printf("Table %v already exists.\n", tableName)
	}

	var customMovie actions.Movie
	customMovie.Title = questioner.Ask("Enter a movie title to add to the table:",
		[]demotools.IAnswerValidator{demotools.NotEmpty{}})
	customMovie.Year = questioner.AskInt("What year was it released?",
		[]demotools.IAnswerValidator{demotools.NotEmpty{}, demotools.InIntRange{
			Lower: 1900, Upper: 2030}})
	customMovie.Info = map[string]interface{}{}
	customMovie.Info["rating"] = questioner.AskFloat64(
		"Enter a rating between 1 and 10:", []demotools.IAnswerValidator{
			demotools.NotEmpty{}, demotools.InFloatRange{Lower: 1, Upper: 10}})
	customMovie.Info["plot"] = questioner.Ask("What's the plot? ",
		[]demotools.IAnswerValidator{demotools.NotEmpty{}})
	err = tableBasics.AddMovie(customMovie)
	if err == nil {
		log.Printf("Added %v to the movie table.\n", customMovie.Title)
	}
	log.Println(strings.Repeat("-", 88))

	log.Printf("Let's update your movie. You previously rated it %v.\n", customMovie.Info["rating"])
	customMovie.Info["rating"] = questioner.AskFloat64(
		"What new rating would you give it?", []demotools.IAnswerValidator{
			demotools.NotEmpty{}, demotools.InFloatRange{Lower: 1, Upper: 10}})
	log.Printf("You summarized the plot as '%v'.\n", customMovie.Info["plot"])
	customMovie.Info["plot"] = questioner.Ask("What would you say now?",
		[]demotools.IAnswerValidator{demotools.NotEmpty{}})
	attributes, err := tableBasics.UpdateMovie(customMovie)
	if err == nil {
		log.Printf("Updated %v with new values.\n", customMovie.Title)
		for _, attVal := range attributes {
			for valKey, val := range attVal {
				log.Printf("\t%v: %v\n", valKey, val)
			}
		}
	}
	log.Println(strings.Repeat("-", 88))

	log.Printf("Getting movie data from %v and adding 250 movies to the table...\n",
		movieSampler.GetURL())
	movies := movieSampler.GetSampleMovies()
	written, err := tableBasics.AddMovieBatch(movies, 250)
	if err != nil {
		panic(err)
	} else {
		log.Printf("Added %v movies to the table.\n", written)
	}

	show := 10
	if show > written {
		show = written
	}
	log.Printf("The first %v movies in the table are:", show)
	for index, movie := range movies[:show] {
		log.Printf("\t%v. %v\n", index+1, movie.Title)
	}
	movieIndex := questioner.AskInt(
		"Enter the number of a movie to get info about it: ", []demotools.IAnswerValidator{
			demotools.InIntRange{Lower: 1, Upper: show}},
	)
	movie, err := tableBasics.GetMovie(movies[movieIndex-1].Title, movies[movieIndex-1].Year)
	if err == nil {
		log.Println(movie)
	}
	log.Println(strings.Repeat("-", 88))

	log.Println("Let's get a list of movies released in a given year.")
	releaseYear := questioner.AskInt("Enter a year between 1972 and 2018: ",
		[]demotools.IAnswerValidator{demotools.InIntRange{Lower: 1972, Upper: 2018}},
	)
	releases, err := tableBasics.Query(releaseYear)
	if err == nil {
		if len(releases) == 0 {
			log.Printf("I couldn't find any movies released in %v!\n", releaseYear)
		} else {
			for _, movie = range releases {
				log.Println(movie)
			}
		}
	}
	log.Println(strings.Repeat("-", 88))

	log.Println("Now let's scan for movies released in a range of years.")
	startYear := questioner.AskInt("Enter a year: ", []demotools.IAnswerValidator{
		demotools.InIntRange{Lower: 1972, Upper: 2018}})
	endYear := questioner.AskInt("Enter another year: ", []demotools.IAnswerValidator{
		demotools.InIntRange{Lower: 1972, Upper: 2018}})
	releases, err = tableBasics.Scan(startYear, endYear)
	if err == nil {
		if len(releases) == 0 {
			log.Printf("I couldn't find any movies released between %v and %v!\n", startYear, endYear)
		} else {
			log.Printf("Found %v movies. In this list, the plot is <nil> because "+
				"we used a projection expression when scanning for items to return only "+
				"the title, year, and rating.\n", len(releases))
			for _, movie = range releases {
				log.Println(movie)
			}
		}
	}
	log.Println(strings.Repeat("-", 88))

	var tables []string
	if questioner.AskBool("Do you want to list all of your tables? (y/n) ", "y") {
		tables, err = tableBasics.ListTables()
		if err == nil {
			log.Printf("Found %v tables:", len(tables))
			for _, table := range tables {
				log.Printf("\t%v", table)
			}
		}
	}
	log.Println(strings.Repeat("-", 88))

	log.Printf("Let's remove your movie '%v'.\n", customMovie.Title)
	if questioner.AskBool("Do you want to delete it from the table? (y/n) ", "y") {
		err = tableBasics.DeleteMovie(customMovie)
	}
	if err == nil {
		log.Printf("Deleted %v.\n", customMovie.Title)
	}

	if questioner.AskBool("Delete the table, too? (y/n)", "y") {
		err = tableBasics.DeleteTable()
	} else {
		log.Println("Don't forget to delete the table when you're done or you might " +
			"incur charges on your account.")
	}
	if err == nil {
		log.Printf("Deleted table %v.\n", tableBasics.TableName)
	}

	log.Println(strings.Repeat("-", 88))
	log.Println("Thanks for watching!")
	log.Println(strings.Repeat("-", 88))
}
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/gov2/dynamodb#code-examples)\. 
+ For API details, see the following topics in *AWS SDK for Go API Reference*\.
  + [BatchWriteItem](https://pkg.go.dev/github.com/aws/aws-sdk-go-v2/service/dynamodb#Client.BatchWriteItem)
  + [CreateTable](https://pkg.go.dev/github.com/aws/aws-sdk-go-v2/service/dynamodb#Client.CreateTable)
  + [DeleteItem](https://pkg.go.dev/github.com/aws/aws-sdk-go-v2/service/dynamodb#Client.DeleteItem)
  + [DeleteTable](https://pkg.go.dev/github.com/aws/aws-sdk-go-v2/service/dynamodb#Client.DeleteTable)
  + [DescribeTable](https://pkg.go.dev/github.com/aws/aws-sdk-go-v2/service/dynamodb#Client.DescribeTable)
  + [GetItem](https://pkg.go.dev/github.com/aws/aws-sdk-go-v2/service/dynamodb#Client.GetItem)
  + [PutItem](https://pkg.go.dev/github.com/aws/aws-sdk-go-v2/service/dynamodb#Client.PutItem)
  + [Query](https://pkg.go.dev/github.com/aws/aws-sdk-go-v2/service/dynamodb#Client.Query)
  + [Scan](https://pkg.go.dev/github.com/aws/aws-sdk-go-v2/service/dynamodb#Client.Scan)
  + [UpdateItem](https://pkg.go.dev/github.com/aws/aws-sdk-go-v2/service/dynamodb#Client.UpdateItem)

------
#### [ Java ]

**SDK for Java 2\.x**  
Create a DynamoDB table\.  

```
    // Create a table with a Sort key.
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
```
Create a helper function to download and extract the sample JSON file\.  

```
        // Load data into the table.
        public static void loadData(DynamoDbClient ddb, String tableName, String fileName) throws IOException {

            DynamoDbEnhancedClient enhancedClient = DynamoDbEnhancedClient.builder()
                    .dynamoDbClient(ddb)
                    .build();
            DynamoDbTable<Movies> mappedTable = enhancedClient.table("Movies", TableSchema.fromBean(Movies.class));

            JsonParser parser = new JsonFactory().createParser(new File(fileName));
            com.fasterxml.jackson.databind.JsonNode rootNode = new ObjectMapper().readTree(parser);
            Iterator<JsonNode> iter = rootNode.iterator();
            ObjectNode currentNode;
            int t = 0 ;
            while (iter.hasNext()) {

                // Only add 200 Movies to the table.
                if (t == 200)
                    break ;
                currentNode = (ObjectNode) iter.next();

                int year = currentNode.path("year").asInt();
                String title = currentNode.path("title").asText();
                String info = currentNode.path("info").toString();

                Movies movies = new Movies();
                movies.setYear(year);
                movies.setTitle(title);
                movies.setInfo(info);

                // Put the data into the Amazon DynamoDB Movie table.
                mappedTable.putItem(movies);
                t++;
            }
       }
```
Get an item from a table\.  

```
    public static void getItem(DynamoDbClient ddb) {

            HashMap<String,AttributeValue> keyToGet = new HashMap<String,AttributeValue>();

            keyToGet.put("year", AttributeValue.builder()
                    .n("1933")
                    .build());

            keyToGet.put("title", AttributeValue.builder()
                    .s("King Kong")
                    .build());

            GetItemRequest request = GetItemRequest.builder()
                    .key(keyToGet)
                    .tableName("Movies")
                    .build();

            try {
                Map<String,AttributeValue> returnedItem = ddb.getItem(request).item();

                if (returnedItem != null) {
                    Set<String> keys = returnedItem.keySet();
                    System.out.println("Amazon DynamoDB table attributes: \n");

                    for (String key1 : keys) {
                        System.out.format("%s: %s\n", key1, returnedItem.get(key1).toString());
                    }
                } else {
                    System.out.format("No item found with the key %s!\n", "year");
                }
            } catch (DynamoDbException e) {
                System.err.println(e.getMessage());
                System.exit(1);
            }
    }
```
Full example\.  

```
public class Scenario {

    public static void main(String[] args) throws IOException {

        final String USAGE = "\n" +
                "Usage:\n" +
                "    <fileName>\n\n" +
                "Where:\n" +
                "    fileName - the path to the moviedata.json file that you can download from the Amazon DynamoDB Developer Guide.\n" ;

        if (args.length != 1) {
              System.out.println(USAGE);
              System.exit(1);
        }

        String tableName = "Movies";
        String fileName = args[0];
        Region region = Region.US_EAST_1;
        DynamoDbClient ddb = DynamoDbClient.builder()
                    .region(region)
                    .build();

        System.out.println("******* Creating an Amazon DynamoDB table named Movies with a key named year and a sort key named title.");
        createTable(ddb, tableName);

        System.out.println("******* Loading data into the Amazon DynamoDB table.");
        loadData(ddb, tableName, fileName);

        System.out.println("******* Getting data from the Movie table.");
        getItem(ddb) ;

        System.out.println("******* Putting a record into the Amazon DynamoDB table.");
        putRecord(ddb);

        System.out.println("******* Updating a record.");
        updateTableItem(ddb, tableName);

        System.out.println("******* Scanning the Amazon DynamoDB table.");
        scanMovies(ddb, tableName);

        System.out.println("******* Querying the Movies released in 2013.");
        queryTable(ddb);

        System.out.println("******* Deleting the Amazon DynamoDB table.");
        deleteDynamoDBTable(ddb, tableName);
        ddb.close();
    }

    // Create a table with a Sort key.
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

    // Query the table.
    public static void queryTable(DynamoDbClient ddb) {
            try {

                DynamoDbEnhancedClient enhancedClient = DynamoDbEnhancedClient.builder()
                        .dynamoDbClient(ddb)
                        .build();

                DynamoDbTable<Movies> custTable = enhancedClient.table("Movies", TableSchema.fromBean(Movies.class));

                    QueryConditional queryConditional = QueryConditional
                            .keyEqualTo(Key.builder()
                                    .partitionValue(2013)
                                    .build());

                    // Get items in the table and write out the ID value
                    Iterator<Movies> results = custTable.query(queryConditional).items().iterator();
                    String result="";

                    while (results.hasNext()) {
                        Movies rec = results.next();
                        System.out.println("The title of the movie is "+rec.getTitle());
                        System.out.println("The movie information  is "+rec.getInfo());
                    }

                } catch (DynamoDbException e) {
                    System.err.println(e.getMessage());
                    System.exit(1);
                }
        }

        // Scan the table.
        public static void scanMovies(DynamoDbClient ddb, String tableName) {

            System.out.println("******* Scanning all movies.\n");

            try{
                DynamoDbEnhancedClient enhancedClient = DynamoDbEnhancedClient.builder()
                        .dynamoDbClient(ddb)
                        .build();

                DynamoDbTable<Movies> custTable = enhancedClient.table("Movies", TableSchema.fromBean(Movies.class));
                Iterator<Movies> results = custTable.scan().items().iterator();
                while (results.hasNext()) {

                    Movies rec = results.next();
                    System.out.println("The movie title is "+rec.getTitle());
                    System.out.println("The movie year is " +rec.getYear());
                }

            } catch (DynamoDbException e) {
                System.err.println(e.getMessage());
                System.exit(1);
            }
        }

        // Load data into the table.
        public static void loadData(DynamoDbClient ddb, String tableName, String fileName) throws IOException {

            DynamoDbEnhancedClient enhancedClient = DynamoDbEnhancedClient.builder()
                    .dynamoDbClient(ddb)
                    .build();
            DynamoDbTable<Movies> mappedTable = enhancedClient.table("Movies", TableSchema.fromBean(Movies.class));

            JsonParser parser = new JsonFactory().createParser(new File(fileName));
            com.fasterxml.jackson.databind.JsonNode rootNode = new ObjectMapper().readTree(parser);
            Iterator<JsonNode> iter = rootNode.iterator();
            ObjectNode currentNode;
            int t = 0 ;
            while (iter.hasNext()) {

                // Only add 200 Movies to the table.
                if (t == 200)
                    break ;
                currentNode = (ObjectNode) iter.next();

                int year = currentNode.path("year").asInt();
                String title = currentNode.path("title").asText();
                String info = currentNode.path("info").toString();

                Movies movies = new Movies();
                movies.setYear(year);
                movies.setTitle(title);
                movies.setInfo(info);

                // Put the data into the Amazon DynamoDB Movie table.
                mappedTable.putItem(movies);
                t++;
            }
       }


    // Update the record to include show only directors.
    public static void updateTableItem(DynamoDbClient ddb, String tableName){

        HashMap<String,AttributeValue> itemKey = new HashMap<String,AttributeValue>();

        // Specify the key and sort key.
        itemKey.put("year", AttributeValue.builder().n("1933").build());
        itemKey.put("title", AttributeValue.builder().s("King Kong").build());

        HashMap<String,AttributeValueUpdate> updatedValues =
                new HashMap<String,AttributeValueUpdate>();

        // Update the column specified by info with updatedVal.
        updatedValues.put("info", AttributeValueUpdate.builder()
                .value(AttributeValue.builder().s("{\"directors\":[\"Merian C. Cooper\",\"Ernest B. Schoedsack\"]").build())
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

        System.out.println("Item was updated!");
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

    public static void putRecord(DynamoDbClient ddb) {

        try {

            // Create a DynamoDbEnhancedClient.
            DynamoDbEnhancedClient enhancedClient = DynamoDbEnhancedClient.builder()
                    .dynamoDbClient(ddb)
                    .build();

            // Create a DynamoDbTable object.
            DynamoDbTable<Movies> table = enhancedClient.table("Movies", TableSchema.fromBean(Movies.class));

            // Populate the Table.
            Movies record = new Movies();
            record.setYear(2020);
            record.setTitle("My Movie2");
            record.setInfo("no info");

            // Put the data into a DynamoDB table.
            table.putItem(record);

        } catch (DynamoDbException e) {
            System.err.println(e.getMessage());
            System.exit(1);
        }
        System.out.println("Added a new issue to the table.");
    }

    public static void getItem(DynamoDbClient ddb) {

            HashMap<String,AttributeValue> keyToGet = new HashMap<String,AttributeValue>();

            keyToGet.put("year", AttributeValue.builder()
                    .n("1933")
                    .build());

            keyToGet.put("title", AttributeValue.builder()
                    .s("King Kong")
                    .build());

            GetItemRequest request = GetItemRequest.builder()
                    .key(keyToGet)
                    .tableName("Movies")
                    .build();

            try {
                Map<String,AttributeValue> returnedItem = ddb.getItem(request).item();

                if (returnedItem != null) {
                    Set<String> keys = returnedItem.keySet();
                    System.out.println("Amazon DynamoDB table attributes: \n");

                    for (String key1 : keys) {
                        System.out.format("%s: %s\n", key1, returnedItem.get(key1).toString());
                    }
                } else {
                    System.out.format("No item found with the key %s!\n", "year");
                }
            } catch (DynamoDbException e) {
                System.err.println(e.getMessage());
                System.exit(1);
            }
    }
}
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javav2/example_code/dynamodb#readme)\. 
+ For API details, see the following topics in *AWS SDK for Java 2\.x API Reference*\.
  + [BatchWriteItem](https://docs.aws.amazon.com/goto/SdkForJavaV2/dynamodb-2012-08-10/BatchWriteItem)
  + [CreateTable](https://docs.aws.amazon.com/goto/SdkForJavaV2/dynamodb-2012-08-10/CreateTable)
  + [DeleteItem](https://docs.aws.amazon.com/goto/SdkForJavaV2/dynamodb-2012-08-10/DeleteItem)
  + [DeleteTable](https://docs.aws.amazon.com/goto/SdkForJavaV2/dynamodb-2012-08-10/DeleteTable)
  + [DescribeTable](https://docs.aws.amazon.com/goto/SdkForJavaV2/dynamodb-2012-08-10/DescribeTable)
  + [GetItem](https://docs.aws.amazon.com/goto/SdkForJavaV2/dynamodb-2012-08-10/GetItem)
  + [PutItem](https://docs.aws.amazon.com/goto/SdkForJavaV2/dynamodb-2012-08-10/PutItem)
  + [Query](https://docs.aws.amazon.com/goto/SdkForJavaV2/dynamodb-2012-08-10/Query)
  + [Scan](https://docs.aws.amazon.com/goto/SdkForJavaV2/dynamodb-2012-08-10/Scan)
  + [UpdateItem](https://docs.aws.amazon.com/goto/SdkForJavaV2/dynamodb-2012-08-10/UpdateItem)

------
#### [ JavaScript ]

**SDK for JavaScript V3**  
Create a DynamoDB client\.  

```
// Create the DynamoDB service client module using ES6 syntax.
import { DynamoDBClient } from "@aws-sdk/client-dynamodb";
// Set the AWS Region.
export const REGION = "REGION"; // For example, "us-east-1".
// Create an Amazon DynamoDB service client object.
export const ddbClient = new DynamoDBClient({ region: REGION });
```
Create a DynamoDB document client\.  

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
Run the scenario\.  

```
*/
import fs from "fs";
// A practical functional library used to split the data into segments.
import * as R from "ramda";
import { ddbClient } from "../libs/ddbClient.js";
import {
  CreateTableCommand,
  DeleteTableCommand,
} from "@aws-sdk/client-dynamodb";
import { ddbDocClient } from "../libs/ddbDocClient.js";
import {
  PutCommand,
  GetCommand,
  UpdateCommand,
  BatchWriteCommand,
  DeleteCommand,
  ScanCommand,
  QueryCommand,
} from "@aws-sdk/lib-dynamodb";

if (process.argv.length < 6) {
  console.log(
    "Usage: node dynamodb_basics.js <tableNaame> <newMovieName> <newMovieYear> <existingMovieName> <existingMovieYear> <newMovieRank> <newMoviePlot>\n" +
      "Example: node dynamodb_basics.js newmoviesbrmur newmoviename 2025 200 'MOVIE PLOT DETAILS'"
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
// Set the parameters.
const tableName = process.argv[2];
const newMovieName = process.argv[3];
const newMovieYear = parseInt(process.argv[4]); // parseInt() converts the string into a number.
const existingMovieName = process.argv[5];
const existingMovieYear = parseInt(process.argv[6]);
const newMovieRank = parseInt(process.argv[7]);
const newMoviePlot = process.argv[8];

export const run = async (
  tableName,
  newMovieName,
  newMovieYear,
  existingMovieName,
  existingMovieYear,
  newMovieRank,
  newMoviePlot
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
      const params = {
        TableName: tableName,
        Item: {
          title: newMovieName,
          year: newMovieYear,
        },
      };
      console.log("Adding movie...");
      const data = await ddbDocClient.send(new PutCommand(params));
      console.log("Success - single movie added.");
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
            TableName: tableName,
            Key: {
              title: existingMovieName,
              year: existingMovieYear,
            },
            // Define expressions for the new or updated attributes.
            ProjectionExpression: "#r",
            ExpressionAttributeNames: { "#r": "rank" },
            UpdateExpression: "set info.plot = :p, info.#r = :r",
            ExpressionAttributeValues: {
              ":p": newMoviePlot,
              ":r": newMovieRank,
            },
            ReturnValues: "ALL_NEW",
          };
          console.log("Updating a single movie...");
          const data = await ddbClient.send(new UpdateCommand(params));
          console.log("Success - movie updated.");
          try {
            console.log("Getting movie....");
            const params = {
              TableName: tableName,
              Key: {
                title: existingMovieName,
                year: existingMovieYear,
              },
            };
            const data = await ddbDocClient.send(new GetCommand(params));
            console.log("Success getting item. Item details :", data.Item);
            try {
              const params = {
                TableName: tableName,

                Key: {
                  title: newMovieName,
                  year: newMovieYear,
                },
              };
              const data = await ddbDocClient.send(new DeleteCommand(params));
              console.log("Success - movie deleted.");
              try {
                console.log("Scanning table....");
                const params = {
                  TableName: tableName,
                  ProjectionExpression: "#r, #y, title",
                  ExpressionAttributeNames: { "#r": "rank", "#y": "year" },
                  FilterExpression: "title = :t and #y = :y and info.#r = :r",
                  ExpressionAttributeValues: {
                    ":r": newMovieRank,
                    ":y": existingMovieYear,
                    ":t": existingMovieName,
                  },
                };
                const data = await ddbClient.send(new ScanCommand(params));
                // Loop through and parse the response.
                for (let i = 0; i < data.Items.length; i++) {
                  console.log(
                    "Scan successful. Items with rank of " +
                      newMovieRank +
                      " include\n" +
                      "Year = " +
                      data.Items[i].year +
                      " Title = " +
                      data.Items[i].title
                  );
                }
                try {
                  const params = {
                    ExpressionAttributeNames: { "#r": "rank", "#y": "year" },
                    ProjectionExpression: "#r, #y, title",
                    TableName: tableName,
                    UpdateExpression: "set #r = :r, title = :t, #y = :y",
                    ExpressionAttributeValues: {
                      ":t": existingMovieName,
                      ":y": existingMovieYear,
                      ":r": newMovieRank,
                    },
                    KeyConditionExpression: "title = :t and #y = :y",
                    FilterExpression: "info.#r = :r",
                  };

                  console.log("Querying table...");
                  const data = await ddbDocClient.send(
                    new QueryCommand(params)
                  );
                  // Loop through and parse the response.
                  for (let i = 0; i < data.Items.length; i++) {
                    console.log(
                      "Query successful. Items with rank of " +
                        newMovieRank +
                        " include\n" +
                        "Year = " +
                        data.Items[i].year +
                        " Title = " +
                        data.Items[i].title
                    );
                  }
                  try {
                    console.log("Deleting a movie...");
                    const params = {
                      TableName: tableName,
                      Key: {
                        title: existingMovieName,
                        year: existingMovieYear,
                      },
                    };
                    const data = await ddbDocClient.send(
                      new DeleteCommand(params)
                    );
                    console.log("Success - item deleted");
                    try {
                      console.log("Deleting the table...");
                      const params = {
                        TableName: tableName,
                      };
                      const data = await ddbDocClient.send(
                        new DeleteTableCommand(params)
                      );
                      console.log("Success, table deleted.");
                      return "Run successfully"; // For unit tests.
                    } catch (err) {
                      console.log("Error deleting table. ", err);
                    }
                  } catch (err) {
                    console.log("Error deleting movie. ", err);
                  }
                } catch (err) {
                  console.log("Error querying table. ", err);
                }
              } catch (err) {
                console.log("Error scanning table. ", err);
              }
            } catch (err) {
              console.log("Error deleting movie. ", err);
            }
          } catch (err) {
            console.log("Error getting item. ", err);
          }
        } catch (err) {
          console.log("Error adding movies by batch. ", err);
        }
      } catch (err) {
        console.log("Error updating item. ", err);
      }
    } catch (err) {
      console.log("Error adding a single item. ", err);
    }
  } catch (err) {
    console.log("Error creating table. ", err);
  }
};
run(
  tableName,
  newMovieName,
  newMovieYear,
  existingMovieName,
  existingMovieYear,
  newMovieRank,
  newMoviePlot
);
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javascriptv3/example_code/dynamodb#code-examples)\. 
+ For API details, see the following topics in *AWS SDK for JavaScript API Reference*\.
  + [BatchWriteItem](https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/clients/client-dynamodb/classes/batchwriteitemcommand.html)
  + [CreateTable](https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/clients/client-dynamodb/classes/createtablecommand.html)
  + [DeleteItem](https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/clients/client-dynamodb/classes/deleteitemcommand.html)
  + [DeleteTable](https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/clients/client-dynamodb/classes/deletetablecommand.html)
  + [DescribeTable](https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/clients/client-dynamodb/classes/describetablecommand.html)
  + [GetItem](https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/clients/client-dynamodb/classes/getitemcommand.html)
  + [PutItem](https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/clients/client-dynamodb/classes/putitemcommand.html)
  + [Query](https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/clients/client-dynamodb/classes/querycommand.html)
  + [Scan](https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/clients/client-dynamodb/classes/scancommand.html)
  + [UpdateItem](https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/clients/client-dynamodb/classes/updateitemcommand.html)

------
#### [ Kotlin ]

**SDK for Kotlin**  
This is prerelease documentation for a feature in preview release\. It is subject to change\.
Create a DynamoDB table\.  

```
suspend fun createScenarioTable(tableNameVal: String, key: String) {

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

    DynamoDbClient { region = "us-east-1" }.use { ddb ->

        val response = ddb.createTable(request)
        ddb.waitUntilTableExists { // suspend call
            tableName = tableNameVal
        }
        println("The table was successfully created ${response.tableDescription?.tableArn}")
    }
}
```
Create a helper function to download and extract the sample JSON file\.  

```
// Load data into the table.
suspend fun loadData(tableName: String, fileName:String) {

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
        putMovie(tableName, year, title, info)
        t++
    }
}
suspend fun putMovie(
    tableNameVal: String,
    year: Int,
    title: String,
    info: String
) {
    val itemValues = mutableMapOf<String, AttributeValue>()
    val strVal = year.toString()
    // Add all content to the table.
    itemValues["year"] = AttributeValue.N(strVal)
    itemValues["title"] = AttributeValue.S(title)
    itemValues["info"] =  AttributeValue.S(info)


    val request = PutItemRequest {
        tableName=tableNameVal
        item = itemValues
    }

    DynamoDbClient { region = "us-east-1" }.use { ddb ->
        ddb.putItem(request)
        println("Added $title to the Movie table.")
    }
}
```
Get an item from a table\.  

```
suspend fun getMovie(tableNameVal: String, keyName: String, keyVal: String) {

    val keyToGet = mutableMapOf<String, AttributeValue>()
    keyToGet[keyName] = AttributeValue.N(keyVal)
    keyToGet["title"] = AttributeValue.S("King Kong")

    val request = GetItemRequest {
        key = keyToGet
        tableName = tableNameVal
    }

    DynamoDbClient { region = "us-east-1" }.use { ddb ->
        val returnedItem = ddb.getItem(request)
        val numbersMap = returnedItem.item
        numbersMap?.forEach { key1 ->
            println(key1.key)
            println(key1.value)
        }
    }
}
```
Full example\.  

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

    val tableName = "Movies"

    // Get the moviedata.json from the Amazon DynamoDB Developer Guide.
    val fileName = args[0]
    val partitionAlias = "#a"

    println("Creating an Amazon DynamoDB table named Movies with a key named id and a sort key named title.")
    createScenarioTable(tableName, "year")
    loadData(tableName, fileName)
    getMovie(tableName, "year", "1933")
    scanMovies(tableName)
    val count = queryMovieTable(tableName, "year", partitionAlias)
    println("There are $count Movies released in 2013.")
    deletIssuesTable(tableName)
}

suspend fun createScenarioTable(tableNameVal: String, key: String) {

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

    DynamoDbClient { region = "us-east-1" }.use { ddb ->

        val response = ddb.createTable(request)
        ddb.waitUntilTableExists { // suspend call
            tableName = tableNameVal
        }
        println("The table was successfully created ${response.tableDescription?.tableArn}")
    }
}

// Load data into the table.
suspend fun loadData(tableName: String, fileName:String) {

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
        putMovie(tableName, year, title, info)
        t++
    }
}
suspend fun putMovie(
    tableNameVal: String,
    year: Int,
    title: String,
    info: String
) {
    val itemValues = mutableMapOf<String, AttributeValue>()
    val strVal = year.toString()
    // Add all content to the table.
    itemValues["year"] = AttributeValue.N(strVal)
    itemValues["title"] = AttributeValue.S(title)
    itemValues["info"] =  AttributeValue.S(info)


    val request = PutItemRequest {
        tableName=tableNameVal
        item = itemValues
    }

    DynamoDbClient { region = "us-east-1" }.use { ddb ->
        ddb.putItem(request)
        println("Added $title to the Movie table.")
    }
}


suspend fun getMovie(tableNameVal: String, keyName: String, keyVal: String) {

    val keyToGet = mutableMapOf<String, AttributeValue>()
    keyToGet[keyName] = AttributeValue.N(keyVal)
    keyToGet["title"] = AttributeValue.S("King Kong")

    val request = GetItemRequest {
        key = keyToGet
        tableName = tableNameVal
    }

    DynamoDbClient { region = "us-east-1" }.use { ddb ->
        val returnedItem = ddb.getItem(request)
        val numbersMap = returnedItem.item
        numbersMap?.forEach { key1 ->
            println(key1.key)
            println(key1.value)
        }
    }
}

suspend fun deletIssuesTable(tableNameVal: String) {

    val request = DeleteTableRequest {
        tableName = tableNameVal
    }

    DynamoDbClient { region = "us-east-1" }.use { ddb ->
        ddb.deleteTable(request)
        println("$tableNameVal was deleted")
    }
}

suspend fun queryMovieTable(
    tableNameVal: String,
    partitionKeyName: String,
    partitionAlias: String
): Int {

    val attrNameAlias = mutableMapOf<String, String>()
    attrNameAlias[partitionAlias] = "year"

    // Set up mapping of the partition name with the value.
    val attrValues = mutableMapOf<String, AttributeValue>()
    attrValues[":$partitionKeyName"] = AttributeValue.N("2013")

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

suspend fun scanMovies(tableNameVal: String) {

    val request = ScanRequest {
        tableName = tableNameVal
    }

    DynamoDbClient { region = "us-east-1" }.use { ddb ->
        val response = ddb.scan(request)
        response.items?.forEach { item ->
            item.keys.forEach { key ->
                println("The key name is $key\n")
                println("The value is ${item[key]}")
            }
        }
    }
}
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/kotlin/services/dynamodb#code-examples)\. 
+ For API details, see the following topics in *AWS SDK for Kotlin API reference*\.
  + [BatchWriteItem](https://github.com/awslabs/aws-sdk-kotlin#generating-api-documentation)
  + [CreateTable](https://github.com/awslabs/aws-sdk-kotlin#generating-api-documentation)
  + [DeleteItem](https://github.com/awslabs/aws-sdk-kotlin#generating-api-documentation)
  + [DeleteTable](https://github.com/awslabs/aws-sdk-kotlin#generating-api-documentation)
  + [DescribeTable](https://github.com/awslabs/aws-sdk-kotlin#generating-api-documentation)
  + [GetItem](https://github.com/awslabs/aws-sdk-kotlin#generating-api-documentation)
  + [PutItem](https://github.com/awslabs/aws-sdk-kotlin#generating-api-documentation)
  + [Query](https://github.com/awslabs/aws-sdk-kotlin#generating-api-documentation)
  + [Scan](https://github.com/awslabs/aws-sdk-kotlin#generating-api-documentation)
  + [UpdateItem](https://github.com/awslabs/aws-sdk-kotlin#generating-api-documentation)

------
#### [ Python ]

**SDK for Python \(Boto3\)**  
Create a class that encapsulates a DynamoDB table\.  

```
from decimal import Decimal
from io import BytesIO
import json
import logging
import os
from pprint import pprint
import requests
from zipfile import ZipFile
import boto3
from boto3.dynamodb.conditions import Key
from botocore.exceptions import ClientError
from question import Question

logger = logging.getLogger(__name__)

class Movies:
    """Encapsulates an Amazon DynamoDB table of movie data."""
    def __init__(self, dyn_resource):
        """
        :param dyn_resource: A Boto3 DynamoDB resource.
        """
        self.dyn_resource = dyn_resource
        self.table = None

    def exists(self, table_name):
        """
        Determines whether a table exists. As a side effect, stores the table in
        a member variable.

        :param table_name: The name of the table to check.
        :return: True when the table exists; otherwise, False.
        """
        try:
            table = self.dyn_resource.Table(table_name)
            table.load()
            exists = True
        except ClientError as err:
            if err.response['Error']['Code'] == 'ResourceNotFoundException':
                exists = False
            else:
                logger.error(
                    "Couldn't check for existence of %s. Here's why: %s: %s",
                    table_name,
                    err.response['Error']['Code'], err.response['Error']['Message'])
                raise
        else:
            self.table = table
        return exists

    def create_table(self, table_name):
        """
        Creates an Amazon DynamoDB table that can be used to store movie data.
        The table uses the release year of the movie as the partition key and the
        title as the sort key.

        :param table_name: The name of the table to create.
        :return: The newly created table.
        """
        try:
            self.table = self.dyn_resource.create_table(
                TableName=table_name,
                KeySchema=[
                    {'AttributeName': 'year', 'KeyType': 'HASH'},  # Partition key
                    {'AttributeName': 'title', 'KeyType': 'RANGE'}  # Sort key
                ],
                AttributeDefinitions=[
                    {'AttributeName': 'year', 'AttributeType': 'N'},
                    {'AttributeName': 'title', 'AttributeType': 'S'}
                ],
                ProvisionedThroughput={'ReadCapacityUnits': 10, 'WriteCapacityUnits': 10})
            self.table.wait_until_exists()
        except ClientError as err:
            logger.error(
                "Couldn't create table %s. Here's why: %s: %s", table_name,
                err.response['Error']['Code'], err.response['Error']['Message'])
            raise
        else:
            return self.table

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

    def get_movie(self, title, year):
        """
        Gets movie data from the table for a specific movie.

        :param title: The title of the movie.
        :param year: The release year of the movie.
        :return: The data about the requested movie.
        """
        try:
            response = self.table.get_item(Key={'year': year, 'title': title})
        except ClientError as err:
            logger.error(
                "Couldn't get movie %s from table %s. Here's why: %s: %s",
                title, self.table.name,
                err.response['Error']['Code'], err.response['Error']['Message'])
            raise
        else:
            return response['Item']

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

    def scan_movies(self, year_range):
        """
        Scans for movies that were released in a range of years.
        Uses a projection expression to return a subset of data for each movie.

        :param year_range: The range of years to retrieve.
        :return: The list of movies released in the specified years.
        """
        movies = []
        scan_kwargs = {
            'FilterExpression': Key('year').between(year_range['first'], year_range['second']),
            'ProjectionExpression': "#yr, title, info.rating",
            'ExpressionAttributeNames': {"#yr": "year"}}
        try:
            done = False
            start_key = None
            while not done:
                if start_key:
                    scan_kwargs['ExclusiveStartKey'] = start_key
                response = self.table.scan(**scan_kwargs)
                movies.extend(response.get('Items', []))
                start_key = response.get('LastEvaluatedKey', None)
                done = start_key is None
        except ClientError as err:
            logger.error(
                "Couldn't scan for movies. Here's why: %s: %s",
                err.response['Error']['Code'], err.response['Error']['Message'])
            raise

        return movies

    def delete_movie(self, title, year):
        """
        Deletes a movie from the table.

        :param title: The title of the movie to delete.
        :param year: The release year of the movie to delete.
        """
        try:
            self.table.delete_item(Key={'year': year, 'title': title})
        except ClientError as err:
            logger.error(
                "Couldn't delete movie %s. Here's why: %s: %s", title,
                err.response['Error']['Code'], err.response['Error']['Message'])
            raise

    def delete_table(self):
        """
        Deletes the table.
        """
        try:
            self.table.delete()
            self.table = None
        except ClientError as err:
            logger.error(
                "Couldn't delete table. Here's why: %s: %s",
                err.response['Error']['Code'], err.response['Error']['Message'])
            raise
```
Create a helper function to download and extract the sample JSON file\.  

```
def get_sample_movie_data(movie_file_name):
    """
    Gets sample movie data, either from a local file or by first downloading it from
    the Amazon DynamoDB developer guide.

    :param movie_file_name: The local file name where the movie data is stored in JSON format.
    :return: The movie data as a dict.
    """
    if not os.path.isfile(movie_file_name):
        print(f"Downloading {movie_file_name}...")
        movie_content = requests.get(
            'https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/samples/moviedata.zip')
        movie_zip = ZipFile(BytesIO(movie_content.content))
        movie_zip.extractall()

    try:
        with open(movie_file_name) as movie_file:
            movie_data = json.load(movie_file, parse_float=Decimal)
    except FileNotFoundError:
        print(f"File {movie_file_name} not found. You must first download the file to "
              "run this demo. See the README for instructions.")
        raise
    else:
        # The sample file lists over 4000 movies, return only the first 250.
        return movie_data[:250]
```
Run an interactive scenario to create the table and perform actions on it\.  

```
def run_scenario(table_name, movie_file_name, dyn_resource):
    logging.basicConfig(level=logging.INFO, format='%(levelname)s: %(message)s')

    print('-'*88)
    print("Welcome to the Amazon DynamoDB getting started demo.")
    print('-'*88)

    movies = Movies(dyn_resource)
    movies_exists = movies.exists(table_name)
    if not movies_exists:
        print(f"\nCreating table {table_name}...")
        movies.create_table(table_name)
        print(f"\nCreated table {movies.table.name}.")

    my_movie = Question.ask_questions([
        Question('title', "Enter the title of a movie you want to add to the table: "),
        Question('year', "What year was it released? ", Question.is_int),
        Question(
            'rating', "On a scale of 1 - 10, how do you rate it? ",
            Question.is_float, Question.in_range(1, 10)),
        Question('plot', "Summarize the plot for me: ")
    ])
    movies.add_movie(**my_movie)
    print(f"\nAdded '{my_movie['title']}' to '{movies.table.name}'.")
    print('-'*88)

    movie_update = Question.ask_questions([
        Question(
            'rating',
            f"\nLet's update your movie.\nYou rated it {my_movie['rating']}, what new "
            f"rating would you give it? ", Question.is_float, Question.in_range(1, 10)),
        Question(
            'plot',
            f"You summarized the plot as '{my_movie['plot']}'.\nWhat would you say now? ")])
    my_movie.update(movie_update)
    updated = movies.update_movie(**my_movie)
    print(f"\nUpdated '{my_movie['title']}' with new attributes:")
    pprint(updated)
    print('-'*88)

    if not movies_exists:
        movie_data = get_sample_movie_data(movie_file_name)
        print(f"\nReading data from '{movie_file_name}' into your table.")
        movies.write_batch(movie_data)
        print(f"\nWrote {len(movie_data)} movies into {movies.table.name}.")
    print('-'*88)

    title = "The Lord of the Rings: The Fellowship of the Ring"
    if Question.ask_question(
            f"Let's move on...do you want to get info about '{title}'? (y/n) ",
            Question.is_yesno):
        movie = movies.get_movie(title, 2001)
        print("\nHere's what I found:")
        pprint(movie)
    print('-'*88)

    ask_for_year = True
    while ask_for_year:
        release_year = Question.ask_question(
            f"\nLet's get a list of movies released in a given year. Enter a year between "
            f"1972 and 2018: ", Question.is_int, Question.in_range(1972, 2018))
        releases = movies.query_movies(release_year)
        if releases:
            print(f"There were {len(releases)} movies released in {release_year}:")
            for release in releases:
                print(f"\t{release['title']}")
            ask_for_year = False
        else:
            print(f"I don't know about any movies released in {release_year}!")
            ask_for_year = Question.ask_question("Try another year? (y/n) ", Question.is_yesno)
    print('-'*88)

    years = Question.ask_questions([
        Question(
            'first',
            f"\nNow let's scan for movies released in a range of years. Enter a year: ",
            Question.is_int, Question.in_range(1972, 2018)),
        Question(
            'second', "Now enter another year: ",
            Question.is_int, Question.in_range(1972, 2018))])
    releases = movies.scan_movies(years)
    if releases:
        count = Question.ask_question(
            f"\nFound {len(releases)} movies. How many do you want to see? ",
            Question.is_int, Question.in_range(1, len(releases)))
        print(f"\nHere are your {count} movies:\n")
        pprint(releases[:count])
    else:
        print(f"I don't know about any movies released between {years['first']} "
              f"and {years['second']}.")
    print('-'*88)

    if Question.ask_question(
            f"\nLet's remove your movie from the table. Do you want to remove "
            f"'{my_movie['title']}'? (y/n)", Question.is_yesno):
        movies.delete_movie(my_movie['title'], my_movie['year'])
        print(f"\nRemoved '{my_movie['title']}' from the table.")
    print('-'*88)

    if Question.ask_question(f"\nDelete the table? (y/n) ", Question.is_yesno):
        movies.delete_table()
        print(f"Deleted {table_name}.")
    else:
        print("Don't forget to delete the table when you're done or you might incur "
              "charges on your account.")

    print("\nThanks for watching!")
    print('-'*88)


if __name__ == '__main__':
    try:
        run_scenario(
            'doc-example-table-movies', 'moviedata.json', boto3.resource('dynamodb'))
    except Exception as e:
        print(f"Something went wrong with the demo! Here's what: {e}")
```
This scenario uses the following helper class to ask questions at a command prompt\.  

```
class Question:
    """
    A helper class to ask questions at a command prompt and validate and convert
    the answers.
    """
    def __init__(self, key, question, *validators):
        """
        :param key: The key that is used for storing the answer in a dict, when
                    multiple questions are asked in a set.
        :param question: The question to ask.
        :param validators: The answer is passed through the list of validators until
                           one fails or they all pass. Validators may also convert the
                           answer to another form, such as from a str to an int.
        """
        self.key = key
        self.question = question
        self.validators = Question.non_empty, *validators

    @staticmethod
    def ask_questions(questions):
        """
        Asks a set of questions and stores the answers in a dict.

        :param questions: The list of questions to ask.
        :return: A dict of answers.
        """
        answers = {}
        for question in questions:
            answers[question.key] = Question.ask_question(
                question.question, *question.validators)
        return answers

    @staticmethod
    def ask_question(question, *validators):
        """
        Asks a single question and validates it against a list of validators.
        When an answer fails validation, the complaint is printed and the question
        is asked again.

        :param question: The question to ask.
        :param validators: The list of validators that the answer must pass.
        :return: The answer, converted to its final form by the validators.
        """
        answer = None
        while answer is None:
            answer = input(question)
            for validator in validators:
                answer, complaint = validator(answer)
                if answer is None:
                    print(complaint)
                    break
        return answer

    @staticmethod
    def non_empty(answer):
        """
        Validates that the answer is not empty.
        :return: The non-empty answer, or None.
        """
        return answer if answer != '' else None, "I need an answer. Please?"

    @staticmethod
    def is_yesno(answer):
        """
        Validates a yes/no answer.
        :return: True when the answer is 'y'; otherwise, False.
        """
        return answer.lower() == 'y', ""

    @staticmethod
    def is_int(answer):
        """
        Validates that the answer can be converted to an int.
        :return: The int answer; otherwise, None.
        """
        try:
            int_answer = int(answer)
        except ValueError:
            int_answer = None
        return int_answer, f"{answer} must be a valid integer."

    @staticmethod
    def is_letter(answer):
        """
        Validates that the answer is a letter.
        :return The letter answer, converted to uppercase; otherwise, None.
        """
        return answer.upper() if answer.isalpha() else None, f"{answer} must be a single letter."

    @staticmethod
    def is_float(answer):
        """
        Validate that the answer can be converted to a float.
        :return The float answer; otherwise, None.
        """
        try:
            float_answer = float(answer)
        except ValueError:
            float_answer = None
        return float_answer, f"{answer} must be a valid float."

    @staticmethod
    def in_range(lower, upper):
        """
        Validate that the answer is within a range. The answer must be of a type that can
        be compared to the lower and upper bounds.
        :return: The answer, if it is within the range; otherwise, None.
        """
        def _validate(answer):
            return (
                answer if lower <= answer <= upper else None,
                f"{answer} must be between {lower} and {upper}.")
        return _validate
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/python/example_code/dynamodb#code-examples)\. 
+ For API details, see the following topics in *AWS SDK for Python \(Boto3\) API Reference*\.
  + [BatchWriteItem](https://docs.aws.amazon.com/goto/boto3/dynamodb-2012-08-10/BatchWriteItem)
  + [CreateTable](https://docs.aws.amazon.com/goto/boto3/dynamodb-2012-08-10/CreateTable)
  + [DeleteItem](https://docs.aws.amazon.com/goto/boto3/dynamodb-2012-08-10/DeleteItem)
  + [DeleteTable](https://docs.aws.amazon.com/goto/boto3/dynamodb-2012-08-10/DeleteTable)
  + [DescribeTable](https://docs.aws.amazon.com/goto/boto3/dynamodb-2012-08-10/DescribeTable)
  + [GetItem](https://docs.aws.amazon.com/goto/boto3/dynamodb-2012-08-10/GetItem)
  + [PutItem](https://docs.aws.amazon.com/goto/boto3/dynamodb-2012-08-10/PutItem)
  + [Query](https://docs.aws.amazon.com/goto/boto3/dynamodb-2012-08-10/Query)
  + [Scan](https://docs.aws.amazon.com/goto/boto3/dynamodb-2012-08-10/Scan)
  + [UpdateItem](https://docs.aws.amazon.com/goto/boto3/dynamodb-2012-08-10/UpdateItem)

------
#### [ Ruby ]

**SDK for Ruby**  
Create a class that encapsulates a DynamoDB table\.  

```
require "aws-sdk-dynamodb"
require "json"
require "open-uri"
require "pp"
require "zip"
require_relative "question"

# Encapsulates an Amazon DynamoDB table of movie data.
class Movies
  attr_reader :dynamo_resource
  attr_reader :table

  def initialize(dynamo_resource)
    @dynamo_resource = dynamo_resource
  end

  # Determines whether a table exists. As a side effect, stores the table in
  # a member variable.
  #
  # @param table_name [String] The name of the table to check.
  # @return [Boolean] True when the table exists; otherwise, False.
  def exists?(table_name)
    table = Aws::DynamoDB::Table.new(table_name)
    table.load
    @table = table
  rescue Aws::DynamoDB::Errors::ResourceNotFoundException
    puts("Table #{table_name} doesn't exist. Let's create it.")
    false
  rescue Aws::Errors::ServiceError => e
    puts("Couldn't check for existence of #{table_name}. Here's why:")
    puts("\t#{e.code}: #{e.message}")
    raise
  else
    !@table.nil?
  end

  # Creates an Amazon DynamoDB table that can be used to store movie data.
  # The table uses the release year of the movie as the partition key and the
  # title as the sort key.
  #
  # @param table_name [String] The name of the table to create.
  # @return [Aws::DynamoDB::Table] The newly created table.
  def create_table(table_name)
    @table = @dynamo_resource.create_table(
      table_name: table_name,
      key_schema: [
        {attribute_name: "year", key_type: "HASH"},  # Partition key
        {attribute_name: "title", key_type: "RANGE"}  # Sort key
      ],
      attribute_definitions: [
        {attribute_name: "year", attribute_type: "N"},
        {attribute_name: "title", attribute_type: "S"}
      ],
      provisioned_throughput: {read_capacity_units: 10, write_capacity_units: 10})
    @dynamo_resource.client.wait_until(:table_exists, table_name: table_name)
  rescue Aws::Errors::ServiceError => e
    puts("Couldn't create table #{table_name}. Here's why:")
    puts("\t#{e.code}: #{e.message}")
    raise
  else
    @table
  end

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

  # Gets movie data from the table for a specific movie.
  #
  # @param title [String] The title of the movie.
  # @param year [Integer] The release year of the movie.
  # @return [Hash] The data about the requested movie.
  def get_movie(title, year)
    response = @table.get_item(key: {"year" => year, "title" => title})
  rescue Aws::Errors::ServiceError => e
    puts("Couldn't get movie #{title} from table #{@table.name}. Here's why:")
    puts("\t#{e.code}: #{e.message}")
    raise
  else
    response.item
  end

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

  # Scans for movies that were released in a range of years.
  # Uses a projection expression to return a subset of data for each movie.
  #
  # @param year_range [Hash] The range of years to retrieve.
  # @return [Array] The list of movies released in the specified years.
  def scan_movies(year_range)
    movies = []
    scan_hash = {
      filter_expression: "#yr between :start_yr and :end_yr",
      projection_expression: "#yr, title, info.rating",
      expression_attribute_names: {"#yr" => "year"},
      expression_attribute_values: {
        ":start_yr" => year_range[:start], ":end_yr" => year_range[:end]}
    }
    done = false
    start_key = nil
    until done
      scan_hash[:exclusive_start_key] = start_key unless start_key.nil?
      response = @table.scan(scan_hash)
      movies.concat(response.items) unless response.items.nil?
      start_key = response.last_evaluated_key
      done = start_key.nil?
    end
  rescue Aws::Errors::ServiceError => e
    puts("Couldn't scan for movies. Here's why:")
    puts("\t#{e.code}: #{e.message}")
    raise
  else
    movies
  end

  # Deletes a movie from the table.
  #
  # @param title [String] The title of the movie to delete.
  # @param year [Integer] The release year of the movie to delete.
  def delete_movie(title, year)
    @table.delete_item(key: {"year" => year, "title" => title})
  rescue Aws::Errors::ServiceError => e
    puts("Couldn't delete movie #{title}. Here's why:")
    puts("\t#{e.code}: #{e.message}")
    raise
  end

  # Deletes the table.
  def delete_table
    @table.delete
    @table = nil
  rescue Aws::Errors::ServiceError => e
    puts("Couldn't delete table. Here's why:")
    puts("\t#{e.code}: #{e.message}")
    raise
  end
end
```
Create a helper function to download and extract the sample JSON file\.  

```
# Gets sample movie data, either from a local file or by first downloading it from
# the Amazon DynamoDB Developer Guide.
#
# @param movie_file_name [String] The local file name where the movie data is stored in JSON format.
# @return [Hash] The movie data as a Hash.
def get_sample_movie_data(movie_file_name)
  if !File.file?(movie_file_name)
    puts("Downloading #{movie_file_name}...")
    movie_content = URI.open(
      "https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/samples/moviedata.zip"
    )
    movie_json = ""
    Zip::File.open_buffer(movie_content) do |zip|
      zip.each do |entry|
        movie_json = entry.get_input_stream.read
      end
    end
  else
    movie_json = File.read(movie_file_name)
  end
  movie_data = JSON.parse(movie_json)
  # The sample file lists over 4000 movies. This returns only the first 250.
  movie_data.slice(0, 250)
rescue Errno::ENOENT
  puts("File #{movie_file_name} not found. Before you can run this demo, you must "\
       "download the file. For instructions, see the README.")
  raise
end
```
Run an interactive scenario to create the table and perform actions on it\.  

```
# Runs the DynamoDB getting started demo.
#
# @param movies [Movies] A wrapper class initialized with a DynamoDB resource.
# @param table_name [String] The name to give the movie table.
# @param movie_file_name [String] The name of a file that contains movie data in JSON
#                                 format. This data is loaded into the movie table
#                                 as part of the demo.
def run_scenario(movies, table_name, movie_file_name)
  puts("-" * 88)
  puts("Welcome to the DynamoDB getting started demo.")
  puts("-" * 88)

  movies_exists = movies.exists?(table_name)
  unless movies_exists
    puts("\nCreating table #{table_name}...")
    movies.create_table(table_name)
    puts("\nCreated table #{movies.table.name}.")
  end

  my_movie = {}
  my_movie[:title] = Question.ask("Enter the title of a movie to add to the table: ")
  my_movie[:year] = Question.ask("What year was it released? ", method(:is_int))
  my_movie[:rating] = Question.ask(
    "On a scale of 1 - 10, how do you rate it? ", method(:is_float), in_range(1, 10)
  )
  my_movie[:plot] = Question.ask("Summarize the plot for me: ")

  movies.add_movie(**my_movie)
  puts("\nAdded '#{my_movie[:title]}' to '#{movies.table.name}'.")
  puts("-" * 88)

  puts("Let's update your movie. You rated it #{my_movie[:rating]}.")
  my_movie[:rating] = Question.ask("What new rating would you give it? ",
    method(:is_float), in_range(1, 10))
  puts("You summarized the plot as '#{my_movie[:plot]}'.")
  my_movie[:plot] = Question.ask("What would you say now? ")
  updated = movies.update_movie(**my_movie)
  puts("Updated '#{my_movie[:title]}' with new attributes:")
  pp(updated)
  puts("-" * 88)

  unless movies_exists
    movie_data = get_sample_movie_data(movie_file_name)
    puts("Reading data from '#{movie_file_name}' into your table.")
    movies.write_batch(movie_data)
    puts("Wrote #{movie_data.length} movies into #{movies.table.name}.")
    puts("-" * 88)
  end

  title = "The Lord of the Rings: The Fellowship of the Ring"
  if Question.ask("Let's move on. Do you want to get info about '#{title}'? (y/n) ",
         method(:is_yesno))
    movie = movies.get_movie(title, 2001)
    puts("\nHere's what I found:")
    pp(movie)
    puts("-" * 88)
  end

  ask_for_year = true
  puts("Let's get a list of movies released in a given year.")
  while ask_for_year
    release_year = Question.ask(
      "Enter a year between 1972 and 2018: ", method(:is_int), in_range(1972, 2018))
    releases = movies.query_movies(release_year)
    if !releases.empty?
      puts("There were #{releases.length} movies released in #{release_year}:")
      releases.each do |release|
        puts("\t#{release["title"]}")
        ask_for_year = false
      end
    else
      puts("I don't know about any movies released in #{release_year}!")
      ask_for_year = Question.ask("Try another year? (y/n) ", method(:is_yesno))
      puts("-" * 88)
    end
  end

  years = {}
  years[:start] = Question.ask(
    "Let's scan for movies released in a range of years. Enter a year: ",
    method(:is_int), in_range(1972, 2018))
  years[:end] = Question.ask(
    "Now enter another year: ", method(:is_int), in_range(1972, 2018))
  releases = movies.scan_movies(years)
  if !releases.empty?
    puts("Found #{releases.length} movies.")
    count = Question.ask(
      "How many do you want to see? ", method(:is_int), in_range(1, releases.length))
    puts("Here are your #{count} movies:")
    releases.take(count).each do |release|
      puts("\t#{release["title"]}")
    end
  else
    puts("I don't know about any movies released between #{years[:start]} "\
         "and #{years[:end]}.")
  puts("-" * 88)
  end

  puts("Let's remove your movie from the table.")
  if Question.ask(
    "Do you want to remove '#{my_movie[:title]}'? (y/n) ", method(:is_yesno))
    movies.delete_movie(my_movie[:title], my_movie[:year])
    puts("Removed '#{my_movie[:title]}' from the table.")
    puts("-" * 88)
  end

  if Question.ask("Delete the table? (y/n) ", method(:is_yesno))
    movies.delete_table
    puts("Deleted #{table_name}.")
  else
    puts("Don't forget to delete the table when you're done or you might incur "\
      "charges on your account.")
  end

  puts("\nThanks for watching!")
  puts("-" * 88)
rescue Aws::Errors::ServiceError
  puts("Something went wrong with the demo.")
rescue Errno::ENOENT
  true
end

run_scenario(
  Movies.new(Aws::DynamoDB::Resource.new),
"doc-example-table-movies", "moviedata.json",
  ) if $PROGRAM_NAME == __FILE__
```
This scenario uses the following helper class to ask questions at a command prompt\.  

```
# Asks a single question and validates it against a list of validators.
# When an answer fails validation, the complaint is printed and the question
# is asked again.
#
# @param question [String] The question to ask.
# @param validators [Array] The list of validators that the answer must pass.
# @return The answer, converted to its final form by the validators.
class Question
  def self.ask(question, *validators)
    answer = nil
    while answer.nil?
      puts(question)
      answer = gets.chomp
      validators.unshift(method(:non_empty)) unless validators[0] == method(:non_empty)
      validators.each do |validator|
        answer, complaint = validator.call(answer)
        if answer.nil?
          puts(complaint)
          break
        end
      end
    end
    answer
  end
end

# Validates that the answer is not empty.
# @return [Array] The non-empty answer, or nil.
def non_empty(answer)
  answer = nil unless answer != ""
  [answer, "I need an answer. Please?"]
end

# Validates a yes/no answer.
# @return [Array] True when the answer is 'y'; otherwise, False.
def is_yesno(answer)
  [answer.downcase == "y", ""]
end

# Validates that the answer can be converted to an int.
# @return [Array] The int answer; otherwise, nil.
def is_int(answer)
  int_answer = answer.to_i
  if int_answer == 0
    int_answer = nil
  end
  [int_answer, "#{answer} must be a valid integer."]
end

# Validates that the answer can be converted to a float.
# :return [Array] The float answer; otherwise, None.
def is_float(answer)
  float_answer = answer.to_f
  if float_answer == 0.0
    float_answer = nil
  end
  [float_answer, "#{answer} must be a valid float."]
end

# Validates that the answer is within a range. The answer must be of a type that can
# be compared to the lower and upper bounds.
# @return [Proc] A Proc that can be called to determine whether the answer is within
#                the expected range.
def in_range(lower, upper)
  Proc.new { |answer|
    answer.between?(lower, upper) ? range_answer = answer : range_answer = nil
    [range_answer, "#{answer} must be between #{lower} and #{upper}."]
  }
end
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/ruby/example_code/dynamodb#code-examples)\. 
+ For API details, see the following topics in *AWS SDK for Ruby API Reference*\.
  + [BatchWriteItem](https://docs.aws.amazon.com/goto/SdkForRubyV3/dynamodb-2012-08-10/BatchWriteItem)
  + [CreateTable](https://docs.aws.amazon.com/goto/SdkForRubyV3/dynamodb-2012-08-10/CreateTable)
  + [DeleteItem](https://docs.aws.amazon.com/goto/SdkForRubyV3/dynamodb-2012-08-10/DeleteItem)
  + [DeleteTable](https://docs.aws.amazon.com/goto/SdkForRubyV3/dynamodb-2012-08-10/DeleteTable)
  + [DescribeTable](https://docs.aws.amazon.com/goto/SdkForRubyV3/dynamodb-2012-08-10/DescribeTable)
  + [GetItem](https://docs.aws.amazon.com/goto/SdkForRubyV3/dynamodb-2012-08-10/GetItem)
  + [PutItem](https://docs.aws.amazon.com/goto/SdkForRubyV3/dynamodb-2012-08-10/PutItem)
  + [Query](https://docs.aws.amazon.com/goto/SdkForRubyV3/dynamodb-2012-08-10/Query)
  + [Scan](https://docs.aws.amazon.com/goto/SdkForRubyV3/dynamodb-2012-08-10/Scan)
  + [UpdateItem](https://docs.aws.amazon.com/goto/SdkForRubyV3/dynamodb-2012-08-10/UpdateItem)

------

For a complete list of AWS SDK developer guides and code examples, see [Using DynamoDB with an AWS SDK](sdk-general-information-section.md)\. This topic also includes information about getting started and details about previous SDK versions\.