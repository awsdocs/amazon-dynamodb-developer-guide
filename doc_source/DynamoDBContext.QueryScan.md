# Example: Query and Scan in DynamoDB Using the AWS SDK for \.NET Object Persistence Model<a name="DynamoDBContext.QueryScan"></a>

The C\# example in this section defines the following classes and maps them to the tables in DynamoDB\. For more information about creating the tables used in this example, see [Creating Tables and Loading Data for Code Examples in DynamoDB](SampleData.md)\.
+ The `Book` class maps to the `ProductCatalog` table\.
+ The `Forum`, `Thread`, and `Reply` classes map to tables of the same name\.

The example then runs the following query and scan operations using `DynamoDBContext`\.
+ Get a book by `Id`\. 

  The `ProductCatalog` table has `Id` as its primary key\. It does not have a sort key as part of its primary key\. Therefore, you cannot query the table\. You can get an item using its `Id` value\. 
+ Run the following queries against the `Reply` table\. \(The `Reply` table's primary key is composed of `Id` and `ReplyDateTime` attributes\. The `ReplyDateTime` is a sort key\. Therefore, you can query this table\.\)
  + Find replies to a forum thread posted in the last 15 days\.
  + Find replies to a forum thread posted in a specific date range\.
+ Scan the `ProductCatalog` table to find books whose price is less than zero\.

  For performance reasons, you should use a query operation instead of a scan operation\. However, there are times you might need to scan a table\. Suppose that there was a data entry error and one of the book prices is set to less than 0\. This example scans the `ProductCategory` table to find book items \(the `ProductCategory` is book\) at price of less than 0\.

 For instructions about creating a working sample, see [\.NET Code Examples](CodeSamples.DotNet.md)\.

**Note**  
 The following example does not work with \.NET core because it does not support synchronous methods\. For more information, see [AWS Asynchronous APIs for \.NET](https://docs.aws.amazon.com/sdk-for-net/v3/developer-guide/sdk-net-async-api.html)\.

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
using System.Configuration;
using Amazon.DynamoDBv2;
using Amazon.DynamoDBv2.DataModel;
using Amazon.DynamoDBv2.DocumentModel;
using Amazon.Runtime;

namespace com.amazonaws.codesamples
{
    class HighLevelQueryAndScan
    {
        private static AmazonDynamoDBClient client = new AmazonDynamoDBClient();

        static void Main(string[] args)
        {
            try
            {
                DynamoDBContext context = new DynamoDBContext(client);
                // Get an item.
                GetBook(context, 101);

                // Sample forum and thread to test queries.
                string forumName = "Amazon DynamoDB";
                string threadSubject = "DynamoDB Thread 1";
                // Sample queries.
                FindRepliesInLast15Days(context, forumName, threadSubject);
                FindRepliesPostedWithinTimePeriod(context, forumName, threadSubject);

                // Scan table.
                FindProductsPricedLessThanZero(context);
                Console.WriteLine("To continue, press Enter");
                Console.ReadLine();
            }
            catch (AmazonDynamoDBException e) { Console.WriteLine(e.Message); }
            catch (AmazonServiceException e) { Console.WriteLine(e.Message); }
            catch (Exception e) { Console.WriteLine(e.Message); }
        }

        private static void GetBook(DynamoDBContext context, int productId)
        {
            Book bookItem = context.Load<Book>(productId);

            Console.WriteLine("\nGetBook: Printing result.....");
            Console.WriteLine("Title: {0} \n No.Of threads:{1} \n No. of messages: {2}",
                      bookItem.Title, bookItem.ISBN, bookItem.PageCount);
        }

        private static void FindRepliesInLast15Days(DynamoDBContext context,
                                string forumName,
                                string threadSubject)
        {
            string replyId = forumName + "#" + threadSubject;
            DateTime twoWeeksAgoDate = DateTime.UtcNow - TimeSpan.FromDays(15);
            IEnumerable<Reply> latestReplies =
                context.Query<Reply>(replyId, QueryOperator.GreaterThan, twoWeeksAgoDate);
            Console.WriteLine("\nFindRepliesInLast15Days: Printing result.....");
            foreach (Reply r in latestReplies)
                Console.WriteLine("{0}\t{1}\t{2}\t{3}", r.Id, r.PostedBy, r.Message, r.ReplyDateTime);
        }

        private static void FindRepliesPostedWithinTimePeriod(DynamoDBContext context,
                                      string forumName,
                                      string threadSubject)
        {
            string forumId = forumName + "#" + threadSubject;
            Console.WriteLine("\nFindRepliesPostedWithinTimePeriod: Printing result.....");

            DateTime startDate = DateTime.UtcNow - TimeSpan.FromDays(30);
            DateTime endDate = DateTime.UtcNow - TimeSpan.FromDays(1);

            IEnumerable<Reply> repliesInAPeriod = context.Query<Reply>(forumId,
                                           QueryOperator.Between, startDate, endDate);
            foreach (Reply r in repliesInAPeriod)
                Console.WriteLine("{0}\t{1}\t{2}\t{3}", r.Id, r.PostedBy, r.Message, r.ReplyDateTime);
        }

        private static void FindProductsPricedLessThanZero(DynamoDBContext context)
        {
            int price = 0;
            IEnumerable<Book> itemsWithWrongPrice = context.Scan<Book>(
                new ScanCondition("Price", ScanOperator.LessThan, price),
                new ScanCondition("ProductCategory", ScanOperator.Equal, "Book")
                );
            Console.WriteLine("\nFindProductsPricedLessThanZero: Printing result.....");
            foreach (Book r in itemsWithWrongPrice)
                Console.WriteLine("{0}\t{1}\t{2}\t{3}", r.Id, r.Title, r.Price, r.ISBN);
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
        // Partition key mapping.
        [DynamoDBHashKey] //Partition key
        public string ForumName
        {
            get; set;
        }
        [DynamoDBRangeKey] //Sort key
        public DateTime Subject
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
        // Explicit mapping (property and table attribute names are different).
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
        [DynamoDBHashKey]
        public string Name
        {
            get; set;
        }
        // All the following properties are explicitly mapped
        // to show how to provide mapping.
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