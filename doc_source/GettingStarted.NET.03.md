# Step 3: Create, Read, Update, and Delete an Item<a name="GettingStarted.NET.03"></a>

In this step, you perform read and write operations on an item in the `Movies` table\.

To learn more about reading and writing data, see [Working with Items in DynamoDB](WorkingWithItems.md)\.

**Topics**
+ [Step 3\.1: Create a New Item](#GettingStarted.NET.03.01)
+ [Step 3\.2: Read an Item](#GettingStarted.NET.03.02)
+ [Step 3\.3: Update an Item](#GettingStarted.NET.03.03)
+ [Step 3\.4: Increment an Atomic Counter](#GettingStarted.NET.03.04)
+ [Step 3\.5: Update an Item \(Conditionally\)](#GettingStarted.NET.03.05)
+ [Step 3\.6: Delete an Item](#GettingStarted.NET.03.06)

## Step 3\.1: Create a New Item<a name="GettingStarted.NET.03.01"></a>

In this step, you add a new item to the `Movies` table\.

1. Copy and paste the following program into the `Program.cs` file, replacing its current contents:

   ```
   using System;
   using System.Collections.Generic;
   using System.Linq;
   using System.Text;
   
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
               // Get a Table object for the table that you created in Step 1
               Table table = GetTableObject("Movies");
               if (table == null)
               {
                   PauseForDebugWindow();
                   return;
               }
   
               // Create a Document representing the movie item to be written to the table
               Document document = new Document();
               document["year"] = 2015;
               document["title"] = "The Big New Movie";
               document["info"] = Document.FromJson("{\"plot\" : \"Nothing happens at all.\",\"rating\" : 0}");
   
               // Use Table.PutItem to write the document item to the table
               try
               {
                   table.PutItem(document);
                   Console.WriteLine("\nPutItem succeeded.\n");
               }
               catch (Exception ex)
               {
                   Console.WriteLine("\n Error: Table.PutItem failed because: " + ex.Message);
                   PauseForDebugWindow();
                   return;
               }
           }
   
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
The primary key is required\. In this table, the primary key is a composite of both a partition\-key attribute \(`year`\) and a sort\-key attribute \(`title`\)\.  
This code writes an item to the table that has the two primary key attributes \(`year` \+ `title`\) and a complex `info` attribute that stores more information about the movie\.

1. Compile and run the program\.

## Step 3\.2: Read an Item<a name="GettingStarted.NET.03.02"></a>

In the previous program, you added the following item to the table:

```
{
   year: 2015,
   title: "The Big New Movie",
   info: { 
        plot: "Nothing happens at all.",
        rating: 0
   }
}
```

You can use the `GetItem` method to read the item from the `Movies` table\. You must specify the primary key values so that you can read any item from `Movies` if you know its `year` and `title`\.

1. Copy and paste the following program into the `Program.cs` file, replacing its current contents:

   ```
   using System;
   using System.Collections.Generic;
   using System.Linq;
   using System.Text;
   
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
               // Get a Table object for the table that you created in Step 1
               Table table = GetTableObject("Movies");
               if (table == null)
               {
                   PauseForDebugWindow();
                   return;
               }
   
               try
               {
                   Document document = table.GetItem(2015, "The Big New Movie");
                   if (document != null)
                       Console.WriteLine("\nGetItem succeeded: \n" + document.ToJsonPretty());
                   else
                       Console.WriteLine("\nGetItem succeeded, but the item was not found");
               }
               catch (Exception e)
               {
                   Console.WriteLine(e.Message);
               }
           }
   
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

1. Compile and run the program\.

## Step 3\.3: Update an Item<a name="GettingStarted.NET.03.03"></a>

You can use the `UpdateItem` method to modify an existing item\. You can update values of existing attributes, add new attributes, or remove attributes\.

In this example, you perform the following updates:
+ Change the value of the existing attributes \(`rating`, `plot`\)\. 
+ Add a new list attribute \(`actors`\) to the existing `info` map\.

The item changes from this:

```
{
   year: 2015,
   title: "The Big New Movie",
   info: { 
        plot: "Nothing happens at all.",
        rating: 0
   }
}
```

To the following:

```
{
   year: 2015,
   title: "The Big New Movie",
   info: { 
           plot: "Everything happens all at once.",
           rating: 5.5,
           actors: ["Larry", "Moe", "Curly"]
   }
}
```

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
               // Get an AmazonDynamoDBClient for the local database
               AmazonDynamoDBClient client = GetLocalClient();
               if (client == null)
               {
                   PauseForDebugWindow();
                   return;
               }
   
               // Create an UpdateItemRequest to modify two existing nested attributes
               // and add a new one
               UpdateItemRequest updateRequest = new UpdateItemRequest()
               {
                   TableName = "Movies",
                   Key = new Dictionary<string, AttributeValue>
               {
                   { "year",  new AttributeValue {
                         N = "2015"
                     } },
                   { "title", new AttributeValue {
                         S = "The Big New Movie"
                     }}
               },
                   ExpressionAttributeValues = new Dictionary<string, AttributeValue>
               {
                   { ":r", new AttributeValue {
                         N = "5.5"
                     } },
                   { ":p", new AttributeValue {
                         S = "Everything happens all at once!"
                     } },
                   { ":a", new AttributeValue {
                         SS = { "Larry","Moe","Curly" }
                     } }
               },
                   UpdateExpression = "SET info.rating = :r, info.plot = :p, info.actors = :a",
                   ReturnValues = "UPDATED_NEW"
               };
   
               // Use AmazonDynamoDBClient.UpdateItem to update the specified attributes
               UpdateItemResponse uir = null;
               try
               {
                   uir = client.UpdateItem(updateRequest);
               }
               catch (Exception ex)
               {
                   Console.WriteLine("\nError: UpdateItem failed, because: " + ex.Message);
                   if (uir != null)
                       Console.WriteLine("    Status code was " + uir.HttpStatusCode.ToString());
                   PauseForDebugWindow();
                   return;
               }
   
               // Get the item from the table and display it to validate that the update succeeded
               DisplayMovieItem(client, "2015", "The Big New Movie");
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
   
           public static void DisplayMovieItem(AmazonDynamoDBClient client, string year, string title)
           {
               // Create Primitives for the HASH and RANGE portions of the primary key
               Primitive hash = new Primitive(year, true);
               Primitive range = new Primitive(title, false);
   
               Table table = null;
               try
               {
                   table = Table.LoadTable(client, "Movies");
               }
               catch (Exception ex)
               {
                   Console.WriteLine("\n Error: failed to load the 'Movies' table; " + ex.Message);
                   return;
               }
               Document document = table.GetItem(hash, range);
               Console.WriteLine("\n The movie record looks like this: \n" + document.ToJsonPretty());
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
Because the document model in the AWS SDK for \.NET doesn't support updating nested attributes, you must use the `AmazonDynamoDBClient.UpdateItem` API instead of `Table.UpdateItem` to update attributes under the top\-level `info` attribute\.  
To do this, create an `UpdateItemRequest` that specifies the item you want to update and the new values you want to set\.  
The `UpdateExpression` is what defines all the updates to be performed on the specified item\.
By setting the `ReturnValues` field to `"UPDATED_NEW"`, you are requesting that DynamoDB return only the updated attributes in the response\.

1. Compile and run the program\.

## Step 3\.4: Increment an Atomic Counter<a name="GettingStarted.NET.03.04"></a>

DynamoDB supports atomic counters, where you use the `UpdateItem` method to increment or decrement the value of an existing attribute without interfering with other write requests\. \(All write requests are applied in the order in which they are received\.\)

The following program increments the `rating` for a movie\. Each time you run it, the program increments this attribute by one\. Again, it is the `UpdateExpression` that determines what happens:

```
UpdateExpression = "SET info.rating = info.rating + :inc",
```

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
               // Get an AmazonDynamoDBClient for the local database
               AmazonDynamoDBClient client = GetLocalClient();
               if (client == null)
               {
                   PauseForDebugWindow();
                   return;
               }
   
               // Create an UpdateItemRequest to modify two existing nested attributes
               // and add a new one
               UpdateItemRequest updateRequest = new UpdateItemRequest()
               {
                   TableName = "Movies",
                   Key = new Dictionary<string, AttributeValue>
               {
                   { "year",  new AttributeValue {
                         N = "2015"
                     } },
                   { "title", new AttributeValue {
                         S = "The Big New Movie"
                     }}
               },
                   ExpressionAttributeValues = new Dictionary<string, AttributeValue>
               {
                   { ":inc", new AttributeValue {
                         N = "1"
                     } }
               },
                   UpdateExpression = "SET info.rating = info.rating + :inc",
                   ReturnValues = "UPDATED_NEW"
               };
   
               // Use AmazonDynamoDBClient.UpdateItem to update the specified attributes
               UpdateItemResponse uir = null;
               try
               {
                   uir = client.UpdateItem(updateRequest);
               }
               catch (Exception ex)
               {
                   Console.WriteLine("\nError: UpdateItem failed, because " + ex.Message);
                   if (uir != null)
                       Console.WriteLine("    Status code was: " + uir.HttpStatusCode.ToString());
                   PauseForDebugWindow();
                   return;
               }
   
               // Get the item from the table and display it to validate that the update succeeded
               DisplayMovieItem(client, "2015", "The Big New Movie");
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
   
           public static void DisplayMovieItem(AmazonDynamoDBClient client, string year, string title)
           {
               // Create Primitives for the HASH and RANGE portions of the primary key
               Primitive hash = new Primitive(year, true);
               Primitive range = new Primitive(title, false);
   
               Table table = null;
               try
               {
                   table = Table.LoadTable(client, "Movies");
               }
               catch (Exception ex)
               {
                   Console.WriteLine("\n Error: failed to load the 'Movies' table; " + ex.Message);
                   return;
               }
               Document document = table.GetItem(hash, range);
               Console.WriteLine("\n The movie record looks like this: \n" + document.ToJsonPretty());
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

1. Compile and run the program\.

## Step 3\.5: Update an Item \(Conditionally\)<a name="GettingStarted.NET.03.05"></a>

The following program shows how to use `UpdateItem` with a condition\. If the condition evaluates to true, the update succeeds; otherwise, the update is not performed\.

In this case, the item is only updated if there are more than three actors in the movie\.

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
               // Get an AmazonDynamoDBClient for the local database
               AmazonDynamoDBClient client = GetLocalClient();
               if (client == null)
               {
                   PauseForDebugWindow();
                   return;
               }
   
               // Create an UpdateItemRequest to modify two existing nested attributes
               // and add a new one
               UpdateItemRequest updateRequest = new UpdateItemRequest()
               {
                   TableName = "Movies",
                   Key = new Dictionary<string, AttributeValue>
               {
                   { "year",  new AttributeValue {
                         N = "2015"
                     } },
                   { "title", new AttributeValue {
                         S = "The Big New Movie"
                     } }
               },
                   ExpressionAttributeValues = new Dictionary<string, AttributeValue>
               {
                   { ":n", new AttributeValue {
                         N = "3"
                     } }
               },
                   ConditionExpression = "size(info.actors) > :n",
                   UpdateExpression = "REMOVE info.actors",
                   ReturnValues = "UPDATED_NEW"
               };
   
               // Use AmazonDynamoDBClient.UpdateItem to update the specified attributes
               UpdateItemResponse uir = null;
               try
               {
                   uir = client.UpdateItem(updateRequest);
               }
               catch (Exception ex)
               {
                   Console.WriteLine("\nError: UpdateItem failed, because:\n   " + ex.Message);
                   if (uir != null)
                       Console.WriteLine("    Status code was " + uir.HttpStatusCode.ToString());
                   PauseForDebugWindow();
                   return;
               }
               if (uir.HttpStatusCode != System.Net.HttpStatusCode.OK)
               {
                   PauseForDebugWindow();
                   return;
               }
   
               // Get the item from the table and display it to validate that the update succeeded
               DisplayMovieItem(client, "2015", "The Big New Movie");
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
   
           public static void DisplayMovieItem(AmazonDynamoDBClient client, string year, string title)
           {
               // Create Primitives for the HASH and RANGE portions of the primary key
               Primitive hash = new Primitive(year, true);
               Primitive range = new Primitive(title, false);
   
               Table table = null;
               try
               {
                   table = Table.LoadTable(client, "Movies");
               }
               catch (Exception ex)
               {
                   Console.WriteLine("\n Error: failed to load the 'Movies' table; " + ex.Message);
                   return;
               }
               Document document = table.GetItem(hash, range);
               Console.WriteLine("\n The movie record looks like this: \n" + document.ToJsonPretty());
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

1. Compile and run the program\.

   The program should fail with the following message:

   `The conditional request failed`

   This is because the movie has three actors in it, but the condition is checking for *greater than* three actors\.

1. Modify the program so that the number of actors that the `ConditionExpression` uses is 2 instead of 3:

   ```
   { ":n", new AttributeValue { N = "2" } }
   ```

   The condition now specifies that the number of actors must be greater than 2\.

1. When you compile and run the program now, the `UpdateItem` operation should succeed\.

## Step 3\.6: Delete an Item<a name="GettingStarted.NET.03.06"></a>

You can use the `Table.DeleteItem` operation to delete an item by specifying its primary key\. You can optionally provide a condition in the `DeleteItemOperationConfig` parameter, to prevent the item from being deleted if the condition is not met\.

In the following example, you try to delete a specific movie item if its rating is 5 or less\.

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
               // Get a Table object for the table that you created in Step 1
               Table table = GetTableObject("Movies");
               if (table == null)
                   return;
   
               // Create the condition
               DeleteItemOperationConfig opConfig = new DeleteItemOperationConfig();
               opConfig.ConditionalExpression = new Expression();
               opConfig.ConditionalExpression.ExpressionAttributeValues[":val"] = 5.0;
               opConfig.ConditionalExpression.ExpressionStatement = "info.rating <= :val";
   
               // Delete this item
               try
               {
                   table.DeleteItem(2015, "The Big New Movie", opConfig);
               }
               catch (Exception ex)
               {
                   Console.WriteLine("\n Error: Could not delete the movie item with year={0}, title=\"{1}\"\n   Reason: {2}.",
                              2015, "The Big New Movie", ex.Message);
               }
   
               // Try to retrieve it, to see if it has been deleted
               Document document = table.GetItem(2015, "The Big New Movie");
               if (document == null)
                   Console.WriteLine("\n The movie item with year={0}, title=\"{1}\" has been deleted.",
                              2015, "The Big New Movie");
               else
                   Console.WriteLine("\nRead back the item: \n" + document.ToJsonPretty());
   
               // Keep the console open if in Debug mode...
               Console.Write("\n\n ...Press any key to continue");
               Console.ReadKey();
               Console.WriteLine();
           }
   
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
       }
   }
   ```

1. Compile and run the program\.

   The program should fail with the following message:

   `The conditional request failed`

   This is because the rating for this particular move is greater than 5\.

1. Modify the program to remove the `DeleteItemOperationConfig` named `opConfig` from the call to `table.DeleteItem`:

   ```
   try { table.DeleteItem( 2015, "The Big New Movie" ); }
   ```

1. Compile and run the program\. Now, the delete succeeds because you removed the condition\.