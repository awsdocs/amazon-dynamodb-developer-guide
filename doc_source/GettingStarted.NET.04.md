# Step 4: Query and Scan the Data<a name="GettingStarted.NET.04"></a>

You can use the `Query` method to retrieve data from a table\. You must specify a partition key value; the sort key is optional\.

The primary key for the `Movies` table is composed of the following:

+ `year` – The partition key\. The attribute type is number\. 

+ `title` – The sort key\. The attribute type is string\.

To find all movies released during a year, you need to specify only the `year` partition\-key attribute\. You can add the `title` sort\-key attribute to retrieve a subset of movies based on some condition \(on the sort\-key attribute\), such as finding movies released in 2014 that have a title starting with the letter "A"\.

In addition to `Query`, there is also a `Scan` method that can retrieve all of the table data\.

To learn more about querying and scanning data, see [Working with Queries](Query.md) and [Working with Scans](Scan.md), respectively\.


+ [Step 4\.1: Query](#GettingStarted.NET.04.Query)
+ [Step 4\.2: Scan](#GettingStarted.NET.04.Scan)

## Step 4\.1: Query<a name="GettingStarted.NET.04.Query"></a>

The C\# code included in this step performs the following queries:

+ Retrieves all movies release in `year` 1985\.

+ Retrieves all movies released in `year` 1992, with `title` beginning with the letter "A" through the letter "L"\.

1. Copy and paste the following program into the `Program.cs` file, replacing its current contents:

   ```
   using System;
   using System.Collections.Generic;
   using System.Linq;
   using System.Text;
   using System.Threading.Tasks;
   
   using Amazon;
   using Amazon.DynamoDBv2;
   using Amazon.DynamoDBv2.Model;
   using Amazon.DynamoDBv2.DocumentModel;
   
   namespace DynamoDB_intro
   {
       class Program
       {
           static string commaSep = ", ";
           static string movieFormatString = "    \"{0}\", lead actor: {1}, genres: {2}";
   
           static void Main(string[] args)
           {
               // Get an AmazonDynamoDBClient for the local DynamoDB database
               AmazonDynamoDBClient client = GetLocalClient();
   
               // Get a Table object for the table that you created in Step 1
               Table table = GetTableObject(client, "Movies");
               if (table == null)
               {
                   PauseForDebugWindow();
                   return;
               }
   
               /*-----------------------------------------------------------------------
                *  4.1.1:  Call Table.Query to initiate a query for all movies with
                *          year == 1985, using an empty filter expression.
                *-----------------------------------------------------------------------*/
               Search search;
               try
               {
                   search = table.Query(1985, new Expression());
               }
               catch (Exception ex)
               {
                   Console.WriteLine("\n Error: 1985 query failed because: " + ex.Message);
                   PauseForDebugWindow();
                   return;
               }
   
               // Display the titles of the movies returned by this query
               List<Document> docList = new List<Document>();
               Console.WriteLine("\n All movies released in 1985:" +
                          "\n-----------------------------------------------");
               do
               {
                   try { docList = search.GetNextSet(); }
                   catch (Exception ex)
                   {
                       Console.WriteLine("\n Error: Search.GetNextStep failed because: " + ex.Message);
                       break;
                   }
                   foreach (var doc in docList)
                       Console.WriteLine("    " + doc["title"]);
               } while (!search.IsDone);
   
   
               /*-----------------------------------------------------------------------
                *  4.1.2a:  Call Table.Query to initiate a query for all movies where
                *           year equals 1992 AND title is between "B" and "Hzzz",
                *           returning the lead actor and genres of each.
                *-----------------------------------------------------------------------*/
               Primitive y_1992 = new Primitive("1992", true);
               QueryOperationConfig config = new QueryOperationConfig();
               config.Filter = new QueryFilter();
               config.Filter.AddCondition("year", QueryOperator.Equal, new DynamoDBEntry[] { 1992 });
               config.Filter.AddCondition("title", QueryOperator.Between, new DynamoDBEntry[] { "B", "Hzz" });
               config.AttributesToGet = new List<string> { "title", "info" };
               config.Select = SelectValues.SpecificAttributes;
   
               try
               {
                   search = table.Query(config);
               }
               catch (Exception ex)
               {
                   Console.WriteLine("\n Error: 1992 query failed because: " + ex.Message);
                   PauseForDebugWindow();
                   return;
               }
   
               // Display the movie information returned by this query
               Console.WriteLine("\n\n Movies released in 1992 with titles between \"B\" and \"Hzz\" (Document Model):" +
                          "\n-----------------------------------------------------------------------------");
               docList = new List<Document>();
               Document infoDoc;
               do
               {
                   try
                   {
                       docList = search.GetNextSet();
                   }
                   catch (Exception ex)
                   {
                       Console.WriteLine("\n Error: Search.GetNextStep failed because: " + ex.Message);
                       break;
                   }
                   foreach (var doc in docList)
                   {
                       infoDoc = doc["info"].AsDocument();
                       Console.WriteLine(movieFormatString,
                                  doc["title"],
                                  infoDoc["actors"].AsArrayOfString()[0],
                                  string.Join(commaSep, infoDoc["genres"].AsArrayOfString()));
                   }
               } while (!search.IsDone);
   
   
               /*-----------------------------------------------------------------------
                *  4.1.2b:  Call AmazonDynamoDBClient.Query to initiate a query for all
                *           movies where year equals 1992 AND title is between M and Tzz,
                *           returning the genres and the lead actor of each.
                *-----------------------------------------------------------------------*/
               QueryRequest qRequest = new QueryRequest
               {
                   TableName = "Movies",
                   ExpressionAttributeNames = new Dictionary<string, string>
               {
                   { "#yr", "year" }
               },
                   ExpressionAttributeValues = new Dictionary<string, AttributeValue>
               {
                   { ":y_1992",  new AttributeValue {
                         N = "1992"
                     } },
                   { ":M",       new AttributeValue {
                         S = "M"
                     } },
                   { ":Tzz",     new AttributeValue {
                         S = "Tzz"
                     } }
               },
                   KeyConditionExpression = "#yr = :y_1992 and title between :M and :Tzz",
                   ProjectionExpression = "title, info.actors[0], info.genres"
               };
   
               QueryResponse qResponse;
               try
               {
                   qResponse = client.Query(qRequest);
               }
               catch (Exception ex)
               {
                   Console.WriteLine("\n Error: Low-level query failed, because: " + ex.Message);
                   PauseForDebugWindow();
                   return;
               }
   
               // Display the movie information returned by this query
               Console.WriteLine("\n\n Movies released in 1992 with titles between \"M\" and \"Tzz\" (low-level):" +
                          "\n-------------------------------------------------------------------------");
               foreach (Dictionary<string, AttributeValue> item in qResponse.Items)
               {
                   Dictionary<string, AttributeValue> info = item["info"].M;
                   Console.WriteLine(movieFormatString,
                              item["title"].S,
                              info["actors"].L[0].S,
                              GetDdbListAsString(info["genres"].L));
               }
           }
   
           public static string GetDdbListAsString(List<AttributeValue> strList)
           {
               StringBuilder sb = new StringBuilder();
               string str = null;
               AttributeValue av;
               for (int i = 0; i < strList.Count; i++)
               {
                   av = strList[i];
                   if (av.S != null)
                       str = av.S;
                   else if (av.N != null)
                       str = av.N;
                   else if (av.SS != null)
                       str = string.Join(commaSep, av.SS.ToArray());
                   else if (av.NS != null)
                       str = string.Join(commaSep, av.NS.ToArray());
                   if (str != null)
                   {
                       if (i > 0)
                           sb.Append(commaSep);
                       sb.Append(str);
                   }
               }
               return (sb.ToString());
           }
   
           public static AmazonDynamoDBClient GetLocalClient()
           {
               // First, set up a DynamoDB client for DynamoDB Local
               AmazonDynamoDBConfig ddbConfig = new AmazonDynamoDBConfig();
               ddbConfig.ServiceURL = "http://localhost:8000";
               AmazonDynamoDBClient client;
               try
               {
                   client = new AmazonDynamoDBClient(ddbConfig);
               }
               catch (Exception ex)
               {
                   Console.WriteLine("\n Error: failed to create a DynamoDB client; " + ex.Message);
                   return (null);
               }
               return (client);
           }
   
   
           public static Table GetTableObject(AmazonDynamoDBClient client, string tableName)
           {
               Table table = null;
               try
               {
                   table = Table.LoadTable(client, tableName);
               }
               catch (Exception ex)
               {
                   Console.WriteLine("\n Error: failed to load the 'Movies' table; " + ex.Message);
                   return (null);
               }
               return (table);
           }
   
           public static void PauseForDebugWindow()
           {
               // Keep the console open if in Debug mode...
               Console.Write("\n\n ...Press any key to continue");
               Console.ReadKey();
               Console.WriteLine();
           }
       }
   }
   ```
**Note**  
In the first query, for all movies released in 1985, an empty expression indicates that filtering on the sort\-key part of the primary key is not wanted\.
In the second query, which uses the AWS SDK for \.NET document model to query for all movies released in 1992 with titles starting with the letters A through L, you can query only for top\-level attributes\. So you must retrieve the entire `info` attribute\. The display code then accesses the nested attributes you're interested in\.
In the third query, you use the low\-level AWS SDK for \.NET API, which gives more control over what is returned\. Here, you can retrieve only those nested attributes within the `info` attribute that you're interested in, namely `info.genres` and `info.actors[0]`\.

1. Compile and run the program\.

**Note**  
The preceding program shows how to query a table by its primary key attributes\. In DynamoDB, you can also optionally create one or more secondary indexes on a table, and query those indexes in the same way that you query a table\. Secondary indexes give your applications additional flexibility by allowing queries on non\-key attributes\. For more information, see [Improving Data Access with Secondary Indexes](SecondaryIndexes.md)\. 

## Step 4\.2: Scan<a name="GettingStarted.NET.04.Scan"></a>

The `Scan` method reads every item in the table and returns all the data in the table\. You can provide an optional `filter_expression` so that only the items matching your criteria are returned\. However, the filter is applied only after the entire table has been scanned\.

The following program scans the entire `Movies` table, which contains approximately 5,000 items\. The scan specifies the optional filter to retrieve only the movies from the 1950s \(approximately 100 items\) and discard all the others\. 

1. Copy and paste the following program into the `Program.cs` file, replacing its current contents:

   ```
   using System;
   using System.Collections.Generic;
   using System.Linq;
   using System.Text;
   using System.Threading.Tasks;
   
   using Amazon;
   using Amazon.DynamoDBv2;
   using Amazon.DynamoDBv2.Model;
   using Amazon.DynamoDBv2.DocumentModel;
   
   namespace DynamoDB_intro
   {
       class Program
       {
           static void Main(string[] args)
           {
               // Get an AmazonDynamoDBClient for the local DynamoDB database
               AmazonDynamoDBClient client = GetLocalClient();
   
               // Get a Table object for the table that you created in Step 1
               Table table = GetTableObject(client, "Movies");
               if (table == null)
               {
                   PauseForDebugWindow();
                   return;
               }
   
   
               /*-----------------------------------------------------------------------
                *  4.2a:  Call Table.Scan to return the movies released in the 1950's,
                *         displaying title, year, lead actor and lead director.
                *-----------------------------------------------------------------------*/
               ScanFilter filter = new ScanFilter();
               filter.AddCondition("year", ScanOperator.Between, new DynamoDBEntry[] { 1950, 1959 });
               ScanOperationConfig config = new ScanOperationConfig
               {
                   AttributesToGet = new List<string> { "year, title, info" },
                   Filter = filter
               };
               Search search = table.Scan(filter);
   
               // Display the movie information returned by this query
               Console.WriteLine("\n\n Movies released in the 1950's (Document Model):" +
                          "\n--------------------------------------------------");
               List<Document> docList = new List<Document>();
               Document infoDoc;
               string movieFormatString = "    \"{0}\" ({1})-- lead actor: {2}, lead director: {3}";
               do
               {
                   try
                   {
                       docList = search.GetNextSet();
                   }
                   catch (Exception ex)
                   {
                       Console.WriteLine("\n Error: Search.GetNextStep failed because: " + ex.Message);
                       break;
                   }
                   foreach (var doc in docList)
                   {
                       infoDoc = doc["info"].AsDocument();
                       Console.WriteLine(movieFormatString,
                                  doc["title"],
                                  doc["year"],
                                  infoDoc["actors"].AsArrayOfString()[0],
                                  infoDoc["directors"].AsArrayOfString()[0]);
                   }
               } while (!search.IsDone);
   
   
               /*-----------------------------------------------------------------------
                *  4.2b:  Call AmazonDynamoDBClient.Scan to return all movies released
                *         in the 1960's, only downloading the title, year, lead
                *         actor and lead director attributes.
                *-----------------------------------------------------------------------*/
               ScanRequest sRequest = new ScanRequest
               {
                   TableName = "Movies",
                   ExpressionAttributeNames = new Dictionary<string, string>
               {
                   { "#yr", "year" }
               },
                   ExpressionAttributeValues = new Dictionary<string, AttributeValue>
               {
                   { ":y_a", new AttributeValue {
                         N = "1960"
                     } },
                   { ":y_z", new AttributeValue {
                         N = "1969"
                     } },
               },
                   FilterExpression = "#yr between :y_a and :y_z",
                   ProjectionExpression = "#yr, title, info.actors[0], info.directors[0]"
               };
   
               ScanResponse sResponse;
               try
               {
                   sResponse = client.Scan(sRequest);
               }
               catch (Exception ex)
               {
                   Console.WriteLine("\n Error: Low-level scan failed, because: " + ex.Message);
                   PauseForDebugWindow();
                   return;
               }
   
               // Display the movie information returned by this scan
               Console.WriteLine("\n\n Movies released in the 1960's (low-level):" +
                          "\n-------------------------------------------");
               foreach (Dictionary<string, AttributeValue> item in sResponse.Items)
               {
                   Dictionary<string, AttributeValue> info = item["info"].M;
                   Console.WriteLine(movieFormatString,
                              item["title"].S,
                              item["year"].N,
                              info["actors"].L[0].S,
                              info["directors"].L[0].S);
               }
           }
   
           public static AmazonDynamoDBClient GetLocalClient()
           {
               // First, set up a DynamoDB client for DynamoDB Local
               AmazonDynamoDBConfig ddbConfig = new AmazonDynamoDBConfig();
               ddbConfig.ServiceURL = "http://localhost:8000";
               AmazonDynamoDBClient client;
               try
               {
                   client = new AmazonDynamoDBClient(ddbConfig);
               }
               catch (Exception ex)
               {
                   Console.WriteLine("\n Error: failed to create a DynamoDB client; " + ex.Message);
                   return (null);
               }
               return (client);
           }
   
   
           public static Table GetTableObject(AmazonDynamoDBClient client, string tableName)
           {
               Table table = null;
               try
               {
                   table = Table.LoadTable(client, tableName);
               }
               catch (Exception ex)
               {
                   Console.WriteLine("\n Error: failed to load the 'Movies' table; " + ex.Message);
                   return (null);
               }
               return (table);
           }
   
           public static void PauseForDebugWindow()
           {
               // Keep the console open if in Debug mode...
               Console.Write("\n\n ...Press any key to continue");
               Console.ReadKey();
               Console.WriteLine();
           }
       }
   }
   ```

   In the code, note the following:

   + The first scan uses the AWS SDK for \.NET document model to scan the `Movies` table and return movies released in the 1950s\. Because the document model doesn't support nested attributes in the `AttributesToGet` field, you must download the entire `info` attribute to have access to the lead actor and director\.

   + The second scan uses the AWS SDK for \.NET low\-level API to scan the `Movies` table and return movies released in the 1960s\. In this case, you can download only those attribute values in `info` that you're interested in, namely `info.actors[0]` and `info.directors[0]`\.

1. Compile and run the program\.

**Note**  
You can also use the `Scan` operation with any secondary indexes that you have created on the table\. For more information, see [Improving Data Access with Secondary Indexes](SecondaryIndexes.md)\. 