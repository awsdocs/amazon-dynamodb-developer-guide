# Step 1: Create a Table<a name="GettingStarted.NET.01"></a>

The document model in the AWS SDK for \.NET doesn't provide for creating tables, so you have to use the low\-level APIs\. For more information, see [Working with Tables: \.NET](LowLevelDotNetWorkingWithTables.md)\.

In this step, you create a table named `Movies`\. The primary key for the table is composed of the following attributes:

+ `year` – The partition key\. The `AttributeType` is `N` for number\.

+ `title` – The sort key\. The `AttributeType` is `S` for string\.

1. Copy and paste the following program into the `Program.cs` file, replacing its current contents:

   ```
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Text;
   
   using Amazon;
   using Amazon.DynamoDBv2;
   using Amazon.DynamoDBv2.Model;
   using Amazon.DynamoDBv2.DocumentModel;
   
   namespace DynamoDB_intro
   {
       class Program
       {
           public static void Main(string[] args)
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
                   PauseForDebugWindow();
                   return;
               }
   
               // Build a 'CreateTableRequest' for the new table
               CreateTableRequest createRequest = new CreateTableRequest
               {
                   TableName = "Movies",
                   AttributeDefinitions = new List<AttributeDefinition>()
               {
                   new AttributeDefinition
                   {
                       AttributeName = "year",
                       AttributeType = "N"
                   },
                   new AttributeDefinition
                   {
                       AttributeName = "title",
                       AttributeType = "S"
                   }
               },
                   KeySchema = new List<KeySchemaElement>()
               {
                   new KeySchemaElement
                   {
                       AttributeName = "year",
                       KeyType = "HASH"
                   },
                   new KeySchemaElement
                   {
                       AttributeName = "title",
                       KeyType = "RANGE"
                   }
               },
               };
   
               // Provisioned-throughput settings are required even though
               // the local test version of DynamoDB ignores them
               createRequest.ProvisionedThroughput = new ProvisionedThroughput(1, 1);
   
               // Using the DynamoDB client, make a synchronous CreateTable request
               CreateTableResponse createResponse;
               try
               {
                   createResponse = client.CreateTable(createRequest);
               }
               catch (Exception ex)
               {
                   Console.WriteLine("\n Error: failed to create the new table; " + ex.Message);
                   PauseForDebugWindow();
                   return;
               }
   
               // Report the status of the new table...
               Console.WriteLine("\n\n Created the \"Movies\" table successfully!\n    Status of the new table: '{0}'", createResponse.TableDescription.TableStatus);
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
In the `AmazonDynamoDBConfig`, you set the `ServiceURL` to "http://localhost:8000" to create the table in the downloadable version of DynamoDB running on your computer\.
In the `CreateTableRequest`, you specify the table name, along with primary key attributes and their data types\.
The partition\-key portion of the primary key, which determines the logical partition where DynamoDB stores an item, is identified in its `KeySchemaElement` in the `CreateTableRequest` as having `KeyType` "HASH"\.
The sort\-key portion of the primary key, which determines the ordering of items having the same partition\-key value, is identified in its `KeySchemaElement` in the `CreateTableRequest` as having `KeyType` "RANGE"\.
The `ProvisionedThroughput` field is required, even though the downloadable version of DynamoDB ignores it\.

1. Compile and run the program\.

To learn more about managing tables, see [Working with Tables in DynamoDB](WorkingWithTables.md)\.