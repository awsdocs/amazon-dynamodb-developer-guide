# Step 8: Query a DynamoDB Table with \.NET<a name="GettingStarted.NET.08"></a>

In this step of the [Microsoft \.NET and DynamoDB Tutorial](GettingStarted.NET.md), you query the `Movies` table in three different ways\.

**Topics**
+ [Use a Simple Document Model Search to Query for 1985 Movies](#GettingStarted.NET.08.a)
+ [Use a QueryOperationConfig to Create a More Complex Query Search](#GettingStarted.NET.08.b)
+ [Use a Low\-Level Query to Find 1992 Movies with Titles Between 'M\.\.\.' and 'Tzz\.\.\.'](#GettingStarted.NET.08.c)
+ [Next Step](#GettingStarted.NET.08.NextStep)

## Use a Simple Document Model Search to Query for 1985 Movies<a name="GettingStarted.NET.08.a"></a>

To set up a simple document\-model query, the `Main` function in `DynamoDB_intro` creates a `Search` object by calling the `Table.Query` API with 1985 as the *partition key* \(also known as the *hash key*\), and an empty filter `Expression`\.

```
try { search = moviesTable.Query( 1985, new Expression( ) ); }
```

It then waits on `SearchListing_async`, which is implemented in `08_Querying.cs` to retrieve and display the query results\.

```
using System;
using System.Threading.Tasks;
using Amazon.DynamoDBv2.Model;
using Amazon.DynamoDBv2.DocumentModel;
using System.Collections.Generic;

namespace DynamoDB_intro
{
    public static partial class DdbIntro
    {
        public static async Task<List<List<Document>>> SearchListing_async(Search search)
        {
            List<List<Document>> docsList = new List<List<Document>>();
            
            do
            {
                try
                {
                    var getNextBatch = search.GetNextSetAsync();
                    var docList = await getNextBatch;
                    docsList.Add(docList);
                }
                catch (Exception)
                {
                    return null;
                }
            } while (!search.IsDone);
            
            return docsList;
        }

        public static async Task<QueryResponse> ClientQuerying_async(QueryRequest qRequest)
        {
            var response = await Client.QueryAsync(qRequest);
            return response;
        }
    }
```

## Use a QueryOperationConfig to Create a More Complex Query Search<a name="GettingStarted.NET.08.b"></a>

To query for 1992 movies with titles from "B\.\.\." to "Hzz\.\.\.", the `Main` function creates a `QueryOperationConfig` object with a `QueryFilter` and various other fields\.

```
      QueryOperationConfig config = new QueryOperationConfig( );
      config.Filter = new QueryFilter( );
      config.Filter.AddCondition( "year", QueryOperator.Equal, new DynamoDBEntry[ ] { 1992 } );
      config.Filter.AddCondition( "title", QueryOperator.Between, new DynamoDBEntry[ ] { "B", "Hzz" } );
      config.AttributesToGet = new List<string> { "year", "title", "info" };
      config.Select = SelectValues.SpecificAttributes;
```

Once again, it then creates a `Search` object by calling the `Table.Query` API, this time with the `QueryOperationConfig` object as the only parameter\.

And again, it waits on `SearchListing_async` to retrieve and display the query results\.

## Use a Low\-Level Query to Find 1992 Movies with Titles Between 'M\.\.\.' and 'Tzz\.\.\.'<a name="GettingStarted.NET.08.c"></a>

To use a low\-level query to retrieve 1992 movies with titles from "M\.\.\." to "Tzz\.\.\.", the `Main` function creates a `QueryRequest` object\.

```
      QueryRequest qRequest= new QueryRequest
      {
        TableName = "Movies",
        ExpressionAttributeNames = new Dictionary<string, string>
        {
          { "#yr", "year" }
        },
        ExpressionAttributeValues = new Dictionary<string, AttributeValue>
        {
          { ":qYr",   new AttributeValue { N = "1992" } },
          { ":tSt",   new AttributeValue { S = "M" } },
          { ":tEn",   new AttributeValue { S = "Tzz" } }
        },
        KeyConditionExpression = "#yr = :qYr and title between :tSt and :tEn",
        ProjectionExpression = "#yr, title, info.actors[0], info.genres, info.running_time_secs"
      };
```

It then waits on the `ClientQuerying_async` function implemented in the `08_Querying.cs` file\. `ClientQuerying_async` waits in turn on the low\-level DynamoDB method `AmazonDynamoDBClient.QueryAsync` to retrieve the query results\.

**Note**  
Because "year" is a reserved word in DynamoDB, you need to create an alias for it \(here `#yr`\) using `ExpressionAttributeNames` in order to use it in a low\-level expression\.

## Next Step<a name="GettingStarted.NET.08.NextStep"></a>

[Step 9: Scan the Movies Table with \.NET](GettingStarted.NET.09.md)