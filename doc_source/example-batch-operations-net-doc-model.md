# Example: Batch Operations Using the AWS SDK for \.NET Document Model API<a name="example-batch-operations-net-doc-model"></a>

**Topics**
+ [Example: Batch Write Using AWS SDK for \.NET Document Model](#example-batchwrite-NetDocumentAPI)

## Example: Batch Write Using the AWS SDK for \.NET Document Model<a name="example-batchwrite-NetDocumentAPI"></a>

 The following C\# code example illustrates single-table and multi\-table batch write operations\. The example performs the following tasks:
+ Illustrate a single table batch write. It adds two items to the ProductCatalog table\. 
+ Illustrate a multi\-table batch write. It adds an item to both the Forum and Thread tables and deletes an item from the Thread table\.

If you followed the steps in [Creating Tables and Loading Sample Data](SampleData.md), you already have the ProductCatalog, Forum, and Thread tables created\. You can also create these sample tables programmatically\. For more information, see [Creating Example Tables and Uploading Data Using the AWS SDK for \.NET](AppendixSampleDataCodeDotNET.md)\. For step\-by\-step instructions to test the following example, see [\.NET Code Samples](CodeSamples.DotNet.md)\.

**Example**  

```
using System;
using System.Collections.Generic;
using Amazon.DynamoDBv2;
using Amazon.DynamoDBv2.DocumentModel;
using Amazon.Runtime;

namespace com.amazonaws.codesamples
{
    class MidLevelBatchWriteItem
    {
        private static AmazonDynamoDBClient client = new AmazonDynamoDBClient();
        static void Main(string[] args)
        {
            try
            {
                SingleTableBatchWrite();
                MultiTableBatchWrite();
            }
            catch (AmazonDynamoDBException e) { Console.WriteLine(e.Message); }
            catch (AmazonServiceException e) { Console.WriteLine(e.Message); }
            catch (Exception e) { Console.WriteLine(e.Message); }

            Console.WriteLine("To continue, press Enter");
            Console.ReadLine();
        }

        private static void SingleTableBatchWrite()
        {
            Table productCatalog = Table.LoadTable(client, "ProductCatalog");
            var batchWrite = productCatalog.CreateBatchWrite();

            var book1 = new Document();
            book1["Id"] = 902;
            book1["Title"] = "My book1 in batch write using .NET helper classes";
            book1["ISBN"] = "902-11-11-1111";
            book1["Price"] = 10;
            book1["ProductCategory"] = "Book";
            book1["Authors"] = new List<string> { "Author 1", "Author 2", "Author 3" };
            book1["Dimensions"] = "8.5x11x.5";
            book1["InStock"] = new DynamoDBBool(true);
            book1["QuantityOnHand"] = new DynamoDBNull(); //Quantity is unknown at this time

            batchWrite.AddDocumentToPut(book1);
            // Specify delete item using overload that takes product key.
            batchWrite.AddKeyToDelete(12345);
            Console.WriteLine("Performing batch write in SingleTableBatchWrite()");
            batchWrite.Execute();
        }

        private static void MultiTableBatchWrite()
        {
            // 1. Specify the item to add to the Forum table.
            Table forum = Table.LoadTable(client, "Forum");
            var forumBatchWrite = forum.CreateBatchWrite();

            var forum1 = new Document();
            forum1["Name"] = "Test BatchWrite Forum";
            forum1["Threads"] = 0;
            forumBatchWrite.AddDocumentToPut(forum1);


            // 2a. Specify the item to add to the Thread table.
            Table thread = Table.LoadTable(client, "Thread");
            var threadBatchWrite = thread.CreateBatchWrite();

            var thread1 = new Document();
            thread1["ForumName"] = "S3 forum";
            thread1["Subject"] = "My sample question";
            thread1["Message"] = "Message text";
            thread1["KeywordTags"] = new List<string> { "S3", "Bucket" };
            threadBatchWrite.AddDocumentToPut(thread1);

            // 2b. Specify the item to delete from the Thread table.
            threadBatchWrite.AddKeyToDelete("someForumName", "someSubject");

            // 3. Create multi-table batch.
            var superBatch = new MultiTableDocumentBatchWrite();
            superBatch.AddBatch(forumBatchWrite);
            superBatch.AddBatch(threadBatchWrite);
            Console.WriteLine("Performing batch write in MultiTableBatchWrite()");
            superBatch.Execute();
        }
    }
}
```
