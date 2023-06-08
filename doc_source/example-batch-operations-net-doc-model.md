# Example: Batch operations using the AWS SDK for \.NET document model API<a name="example-batch-operations-net-doc-model"></a>

**Topics**
+ [Example: Batch write using the AWS SDK for \.NET document model](#example-batchwrite-NetDocumentAPI)

## Example: Batch write using the AWS SDK for \.NET document model<a name="example-batchwrite-NetDocumentAPI"></a>

 The following C\# code example illustrates single table and multi\-table batch write operations\. The example performs the following tasks:
+ Illustrates a single table batch write\. It adds two items to the `ProductCatalog` table\. 
+ Illustrates a multi\-table batch write\. It adds an item to both the `Forum` and `Thread` tables and deletes an item from the `Thread` table\.

If you followed the steps in [Creating tables and loading data for code examples in DynamoDB](SampleData.md), you already have the `ProductCatalog`, `Forum`, and `Thread` tables created\. You can also create these sample tables programmatically\. For more information, see [Creating example tables and uploading data using the AWS SDK for \.NET](AppendixSampleDataCodeDotNET.md)\. For step\-by\-step instructions for testing the following example, see [\.NET code examples](CodeSamples.DotNet.md)\.

**Example**  

```
/**
 * Copyright 2010-2019 Amazon.com, Inc. or its affiliates. All Rights Reserved.
 *
 * This file is licensed under the Apache License, Version 2.0 (the "License").
 * You may not use this file except in compliance with the License. A copy of
 * the License is located at
 *
 * http://aws.amazon.com/apache2.0/
 *
 * This file is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR
 * CONDITIONS OF ANY KIND, either express or implied. See the License for the
 * specific language governing permissions and limitations under the License.
*/
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
            // Specify delete item using overload that takes PK.
            batchWrite.AddKeyToDelete(12345);
            Console.WriteLine("Performing batch write in SingleTableBatchWrite()");
            batchWrite.Execute();
        }

        private static void MultiTableBatchWrite()
        {
            // 1. Specify item to add in the Forum table.
            Table forum = Table.LoadTable(client, "Forum");
            var forumBatchWrite = forum.CreateBatchWrite();

            var forum1 = new Document();
            forum1["Name"] = "Test BatchWrite Forum";
            forum1["Threads"] = 0;
            forumBatchWrite.AddDocumentToPut(forum1);


            // 2a. Specify item to add in the Thread table.
            Table thread = Table.LoadTable(client, "Thread");
            var threadBatchWrite = thread.CreateBatchWrite();

            var thread1 = new Document();
            thread1["ForumName"] = "S3 forum";
            thread1["Subject"] = "My sample question";
            thread1["Message"] = "Message text";
            thread1["KeywordTags"] = new List<string> { "S3", "Bucket" };
            threadBatchWrite.AddDocumentToPut(thread1);

            // 2b. Specify item to delete from the Thread table.
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