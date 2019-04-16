# Step 5: \(Optional\) Delete the Table<a name="GettingStarted.NET.05"></a>

 To delete the `Movies` table: 

1. Copy and paste the following program into the `Program.cs` file, replacing its current contents.

   ```
   using System;
   using System.Text;
   
   using Amazon.DynamoDBv2;
   using Amazon.DynamoDBv2.Model;
   
   namespace DynamoDB_intro
   {
       class Program
       {
           static void Main(string[] args)
           {
               // Get an AmazonDynamoDBClient for the local DynamoDB database
               AmazonDynamoDBClient client = GetLocalClient();
   
               try
               {
                   client.DeleteTable("Movies");
               }
               catch (Exception ex)
               {
                   Console.WriteLine("\n Error: the \'Movies\" table could not be deleted!\n    Reason: " + ex.Message);
                   Console.Write("\n\n ...Press any key to continue");
                   Console.ReadKey();
                   Console.WriteLine();
                   return;
               }
               Console.WriteLine("\n Deleted the \'Movies\" table successfully!");
           }
   
           public static AmazonDynamoDBClient GetLocalClient()
           {
               // First, set up a DynamoDB client for DynamoDB local
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
       }
   }
   ```

1. Compile and run the program\.
