# Step 2: Load Sample Data<a name="GettingStarted.NET.02"></a>

In this step, you populate the `Movies` table with sample data\.


+ [Step 2\.1: Download the Sample Data File](#GettingStarted.NET.02.01)
+ [Step 2\.2: Load the Sample Data into the Movies Table](#GettingStarted.NET.02.02)

This scenario uses a sample data file that contains information about a few thousand movies from the Internet Movie Database \(IMDb\)\.

The movie data is encoded as JSON\. For each movie, the JSON defines a `year` name\-value pair, a `title` name\-value pair, and a complex `info` object, as shown in the following example: 

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

## Step 2\.1: Download the Sample Data File<a name="GettingStarted.NET.02.01"></a>

1. Download the sample data archive: [moviedata\.zip](samples/moviedata.zip)

1. Extract the data file \(`moviedata.json`\) from the archive\.

1. Copy and paste the`moviedata.json` file into the `bin/Debug` folder of your `DynamoDB_intro` Visual Studio project\.

## Step 2\.2: Load the Sample Data into the Movies Table<a name="GettingStarted.NET.02.02"></a>

Build a program that loads movie data into the table you created in Step 1\.

1. This program uses the open source Newtonsoft `Json.NET` library for deserializing JSON data, licensed under the MIT License \(MIT\) \(see [https://github\.com/JamesNK/Newtonsoft\.Json/blob/master/LICENSE\.md](https://github.com/JamesNK/Newtonsoft.Json/blob/master/LICENSE.md)\)\.

   To load the `Json.NET` library into your project, in Visual Studio, open the **NuGet Package Manager Console** from the **Tools** menu\. Then type the following command at the `PM>` prompt:

   ```
   PM> Install-Package Newtonsoft.Json
   ```

1. Copy and paste the following program into the `Program.cs` file, replacing its current contents:

   ```
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Linq;
   using System.Text;
   
   using Amazon;
   using Amazon.DynamoDBv2;
   using Amazon.DynamoDBv2.Model;
   using Amazon.DynamoDBv2.DocumentModel;
   
   using Newtonsoft;
   using Newtonsoft.Json;
   using Newtonsoft.Json.Linq;
   
   namespace DynamoDB_intro
   {
       class Program
       {
           public static Table GetTableObject(string tableName)
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
   
               // Now, create a Table object for the specified table
               Table table;
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
   
           public static void Main(string[] args)
           {
               // First, read in the JSON data from the moviedate.json file
               StreamReader sr = null;
               JsonTextReader jtr = null;
               JArray movieArray = null;
               try
               {
                   sr = new StreamReader("moviedata.json");
                   jtr = new JsonTextReader(sr);
                   movieArray = (JArray)JToken.ReadFrom(jtr);
               }
               catch (Exception ex)
               {
                   Console.WriteLine("\n Error: could not read from the 'moviedata.json' file, because: " + ex.Message);
                   PauseForDebugWindow();
                   return;
               }
               finally
               {
                   if (jtr != null)
                       jtr.Close();
                   if (sr != null)
                       sr.Close();
               }
   
               // Get a Table object for the table that you created in Step 1
               Table table = GetTableObject("Movies");
               if (table == null)
               {
                   PauseForDebugWindow();
                   return;
               }
   
               // Load the movie data into the table (this could take some time)
               Console.Write("\n   Now writing {0:#,##0} movie records from moviedata.json (might take 15 minutes)...\n   ...completed: ", movieArray.Count);
               for (int i = 0, j = 99; i < movieArray.Count; i++)
               {
                   try
                   {
                       string itemJson = movieArray[i].ToString();
                       Document doc = Document.FromJson(itemJson);
                       table.PutItem(doc);
                   }
                   catch (Exception ex)
                   {
                       Console.WriteLine("\nError: Could not write the movie record #{0:#,##0}, because {1}", i, ex.Message);
                       PauseForDebugWindow();
                       return;
                   }
                   if (i >= j)
                   {
                       j++;
                       Console.Write("{0,5:#,##0}, ", j);
                       if (j % 1000 == 0)
                           Console.Write("\n                 ");
                       j += 99;
                   }
               }
               Console.WriteLine("\n   Finished writing all movie records to DynamoDB!");
               PauseForDebugWindow();
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

1. Now compile the project, leaving it in Debug mode, and run it\.