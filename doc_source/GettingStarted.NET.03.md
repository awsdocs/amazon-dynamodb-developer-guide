# Step 3: Load Sample Data into the DynamoDB Table<a name="GettingStarted.NET.03"></a>

In this step of the [Microsoft \.NET and DynamoDB Tutorial](GettingStarted.NET.md), you populate the new `Movies` table in Amazon DynamoDB with sample data from the Internet Movie Database \(IMDb\)\. This data is stored in JSON format in a local text file named `moviedata.json`\.

For each movie, `moviedata.json` defines a `year` name\-value pair, a `title` name\-value pair, and a complex `info` object, as illustrated by the following example\.

```
{
  "year" : 2013,
  "title" : "Turn It Down, Or Else!",
  "info" : {
    "directors" : [
      "Alice Smith",
      "Bob Jones"
    ],
    "release_date" : "2013-01-18T00:00:00Z",
    "rating" : 6.2,
    "genres" : [
      "Comedy",
      "Drama"
    ],
    "image_url" : "http://ia.media-imdb.com/images/N/O9ERWAU7FS797AJ7LU8HN09AMUP908RLlo5JF90EWR7LJKQ7@@._V1_SX400_.jpg",
    "plot" : "A rock band plays their music at high volumes, annoying the neighbors.",
    "rank" : 11,
    "running_time_secs" : 5215,
    "actors" : [
      "David Matthewman",
      "Ann Thomas",
      "Jonathan G. Neff"
    ]
  }
}
```

Before loading the `moviedata.json` file, the `Main` function in `DynamoDB_intro` checks to determine whether the `Movies` table exists and is still empty\. If so, it waits on an asynchronous `LoadingData_async` function that is implemented in the `03_LoadingData.cs` file\.

```
using System;
using System.IO;
using System.Threading.Tasks;
using Amazon.DynamoDBv2.DocumentModel;

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace DynamoDB_intro
{
    public static partial class DdbIntro
    {
        public static async Task<bool> LoadingData_async(Table table, string filePath)
        {
            var movieArray = await ReadJsonMovieFile_async(filePath);

            if (movieArray != null)
                await LoadJsonMovieData_async(table, movieArray);

            return true;
        }
        
        public static async Task<JArray> ReadJsonMovieFile_async(string jsonMovieFilePath)
        {
            StreamReader sr = null;
            JsonTextReader jtr = null;
            JArray movieArray = null;

            Console.WriteLine("  -- Reading the movies data from a JSON file...");
           
            try
            {
                sr = new StreamReader(jsonMovieFilePath);
                jtr = new JsonTextReader(sr);
                movieArray = (JArray)await JToken.ReadFromAsync(jtr);
            }
            catch (Exception ex)
            {
                Console.WriteLine("     ERROR: could not read the file!\n          Reason: {0}.", ex.Message);
            }
            finally
            {
                jtr?.Close();
                sr?.Close();
            }
            
            return movieArray;
        }

        public static async Task<bool> LoadJsonMovieData_async(Table moviesTable, JArray moviesArray)
        {
            int n = moviesArray.Count;
            Console.Write("     -- Starting to load {0:#,##0} movie records into the Movies table asynchronously...\n" + "" +
              "        Wrote: ", n);
            for (int i = 0, j = 99; i < n; i++)
            {
                try
                {
                    string itemJson = moviesArray[i].ToString();
                    Document doc = Document.FromJson(itemJson);
                    Task putItem = moviesTable.PutItemAsync(doc);
                    if (i >= j)
                    {
                        j++;
                        Console.Write("{0,5:#,##0}, ", j);
                        if (j % 1000 == 0)
                            Console.Write("\n               ");
                        j += 99;
                    }
                    await putItem;
                }
                catch (Exception)
                {
                    return false;
                }
            }

            return true;
        }
    }
```

`LoadingData_async` begins by waiting on `ReadJsonMovieFile_async`\. This function reads the `moviedata.json` file using the open\-source [Newtonsoft Json\.NET library](https://www.newtonsoft.com/json), which is licensed under the [MIT License](https://github.com/JamesNK/Newtonsoft.Json/blob/master/LICENSE.md)\.

When the data has been read successfully, `LoadingData_async` waits on `LoadJsonMovieData_async` to load the movie records into the `Movies` table using the DynamoDB document\-model `Table.PutItemAsync` API\. For information about the document model API, see [\.NET: Document Model](DotNetSDKMidLevel.md)\.

## Next Step<a name="GettingStarted.NET.03.NextStep"></a>

[Step 4: Add a Movie to the DynamoDB Table](GettingStarted.NET.04.md)