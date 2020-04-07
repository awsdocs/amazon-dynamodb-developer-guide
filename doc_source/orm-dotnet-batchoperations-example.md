# Example: Batch Write Operation Using the AWS SDK for \.NET Object Persistence Model<a name="orm-dotnet-batchoperations-example"></a>

The following C\# code example declares `Book`, `Forum`, `Thread`, and `Reply` classes and maps them to Amazon DynamoDB tables using the object persistence model attributes\. 

The example then uses the `DynamoDBContext` to illustrate the following batch write operations:
+ `BatchWrite` object to put and delete book items from the `ProductCatalog` table\.
+ `MultiTableBatchWrite` object to put and delete items from the `Forum` and the `Thread` tables\.

For more information about the tables used in this example, see [Creating Tables and Loading Data for Code Examples in DynamoDB](SampleData.md)\. For step\-by\-step instructions to test the following example, see [\.NET Code Examples](CodeSamples.DotNet.md)\.

**Note**  
 The following example doesn't work with \.NET core because it doesn't support synchronous methods\. For more information, see [AWS Asynchronous APIs for \.NET](https://docs.aws.amazon.com/sdk-for-net/v3/developer-guide/sdk-net-async-api.html)\.

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
using Amazon.DynamoDBv2.DataModel;
using Amazon.Runtime;
using Amazon.SecurityToken;

namespace com.amazonaws.codesamples
{
    class HighLevelBatchWriteItem
    {
        private static AmazonDynamoDBClient client = new AmazonDynamoDBClient();

        static void Main(string[] args)
        {
            try
            {
                DynamoDBContext context = new DynamoDBContext(client);
                SingleTableBatchWrite(context);
                MultiTableBatchWrite(context);
            }
            catch (AmazonServiceException e) { Console.WriteLine(e.Message); }
            catch (Exception e) { Console.WriteLine(e.Message); }

            Console.WriteLine("To continue, press Enter");
            Console.ReadLine();
        }

        private static void SingleTableBatchWrite(DynamoDBContext context)
        {
            Book book1 = new Book
            {
                Id = 902,
                InPublication = true,
                ISBN = "902-11-11-1111",
                PageCount = "100",
                Price = 10,
                ProductCategory = "Book",
                Title = "My book3 in batch write"
            };
            Book book2 = new Book
            {
                Id = 903,
                InPublication = true,
                ISBN = "903-11-11-1111",
                PageCount = "200",
                Price = 10,
                ProductCategory = "Book",
                Title = "My book4 in batch write"
            };

            var bookBatch = context.CreateBatchWrite<Book>();
            bookBatch.AddPutItems(new List<Book> { book1, book2 });

            Console.WriteLine("Performing batch write in SingleTableBatchWrite().");
            bookBatch.Execute();
        }

        private static void MultiTableBatchWrite(DynamoDBContext context)
        {
            // 1. New Forum item.
            Forum newForum = new Forum
            {
                Name = "Test BatchWrite Forum",
                Threads = 0
            };
            var forumBatch = context.CreateBatchWrite<Forum>();
            forumBatch.AddPutItem(newForum);

            // 2. New Thread item.
            Thread newThread = new Thread
            {
                ForumName = "S3 forum",
                Subject = "My sample question",
                KeywordTags = new List<string> { "S3", "Bucket" },
                Message = "Message text"
            };

            DynamoDBOperationConfig config = new DynamoDBOperationConfig();
            config.SkipVersionCheck = true;
            var threadBatch = context.CreateBatchWrite<Thread>(config);
            threadBatch.AddPutItem(newThread);
            threadBatch.AddDeleteKey("some partition key value", "some sort key value");

            var superBatch = new MultiTableBatchWrite(forumBatch, threadBatch);
            Console.WriteLine("Performing batch write in MultiTableBatchWrite().");
            superBatch.Execute();
        }
    }

    [DynamoDBTable("Reply")]
    public class Reply
    {
        [DynamoDBHashKey] //Partition key
        public string Id
        {
            get; set;
        }

        [DynamoDBRangeKey] //Sort key
        public DateTime ReplyDateTime
        {
            get; set;
        }

        // Properties included implicitly.
        public string Message
        {
            get; set;
        }
        // Explicit property mapping with object persistence model attributes.
        [DynamoDBProperty("LastPostedBy")]
        public string PostedBy
        {
            get; set;
        }
        // Property to store version number for optimistic locking.
        [DynamoDBVersion]
        public int? Version
        {
            get; set;
        }
    }

    [DynamoDBTable("Thread")]
    public class Thread
    {
        // PK mapping.
        [DynamoDBHashKey]      //Partition key
        public string ForumName
        {
            get; set;
        }
        [DynamoDBRangeKey]      //Sort key
        public String Subject
        {
            get; set;
        }
        // Implicit mapping.
        public string Message
        {
            get; set;
        }
        public string LastPostedBy
        {
            get; set;
        }
        public int Views
        {
            get; set;
        }
        public int Replies
        {
            get; set;
        }
        public bool Answered
        {
            get; set;
        }
        public DateTime LastPostedDateTime
        {
            get; set;
        }
        // Explicit mapping (property and table attribute names are different.
        [DynamoDBProperty("Tags")]
        public List<string> KeywordTags
        {
            get; set;
        }
        // Property to store version number for optimistic locking.
        [DynamoDBVersion]
        public int? Version
        {
            get; set;
        }
    }

    [DynamoDBTable("Forum")]
    public class Forum
    {
        [DynamoDBHashKey]      //Partition key
        public string Name
        {
            get; set;
        }
        // All the following properties are explicitly mapped,
        // only to show how to provide mapping.
        [DynamoDBProperty]
        public int Threads
        {
            get; set;
        }
        [DynamoDBProperty]
        public int Views
        {
            get; set;
        }
        [DynamoDBProperty]
        public string LastPostBy
        {
            get; set;
        }
        [DynamoDBProperty]
        public DateTime LastPostDateTime
        {
            get; set;
        }
        [DynamoDBProperty]
        public int Messages
        {
            get; set;
        }
    }

    [DynamoDBTable("ProductCatalog")]
    public class Book
    {
        [DynamoDBHashKey] //Partition key
        public int Id
        {
            get; set;
        }
        public string Title
        {
            get; set;
        }
        public string ISBN
        {
            get; set;
        }
        public int Price
        {
            get; set;
        }
        public string PageCount
        {
            get; set;
        }
        public string ProductCategory
        {
            get; set;
        }
        public bool InPublication
        {
            get; set;
        }
    }
}
```