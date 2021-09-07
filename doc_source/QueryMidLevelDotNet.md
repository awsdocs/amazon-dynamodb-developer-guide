# Table\.Query Method in the AWS SDK for \.NET<a name="QueryMidLevelDotNet"></a>

The `Query` method enables you to query your tables\. You can only query the tables that have a composite primary key \(partition key and sort key\)\. If your table's primary key is made of only a partition key, then the `Query` operation is not supported\. By default, `Query` internally performs queries that are eventually consistent\. To learn about the consistency model, see [Read Consistency](HowItWorks.ReadConsistency.md)\. 

The `Query` method provides two overloads\. The minimum required parameters to the `Query` method are a partition key value and a sort key filter\. You can use the following overload to provide these minimum required parameters\.

**Example**  

```
Query(Primitive partitionKey, RangeFilter Filter);
```

For example, the following C\# code queries for all forum replies that were posted in the last 15 days\. 

**Example**  

```
string tableName = "Reply";
Table table = Table.LoadTable(client, tableName);

DateTime twoWeeksAgoDate = DateTime.UtcNow - TimeSpan.FromDays(15);
RangeFilter filter = new RangeFilter(QueryOperator.GreaterThan, twoWeeksAgoDate);
Search search = table.Query("DynamoDB Thread 2", filter);
```

This creates a `Search` object\. You can now call the `Search.GetNextSet` method iteratively to retrieve one page of results at a time, as shown in the following C\# code example\. The code prints the attribute values for each item that the query returns\.

**Example**  

```
List<Document> documentSet = new List<Document>();
do
{
  documentSet = search.GetNextSet();
  foreach (var document in documentSet)
    PrintDocument(document);
} while (!search.IsDone);

    private static void PrintDocument(Document document)
{
  Console.WriteLine();
  foreach (var attribute in document.GetAttributeNames())
  {
    string stringValue = null;
    var value = document[attribute];
    if (value is Primitive)
      stringValue = value.AsPrimitive().Value;
    else if (value is PrimitiveList)
      stringValue = string.Join(",", (from primitive
                                        in value.AsPrimitiveList().Entries
                                      select primitive.Value).ToArray());
    Console.WriteLine("{0} - {1}", attribute, stringValue);
  }
}
```

## Specifying Optional Parameters<a name="QueryMidLevelDotNetOptions"></a>

You can also specify optional parameters for `Query`, such as specifying a list of attributes to retrieve, strongly consistent reads, page size, and the number of items returned per page\.  For a complete list of parameters, see [Query](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Query.html)\. To specify optional parameters, you must use the following overload in which you provide the `QueryOperationConfig` object\.

**Example**  

```
Query(QueryOperationConfig config);
```

Assume that you want to run the query in the preceding example \(retrieve forum replies posted in the last 15 days\)\. However, assume that you want to provide optional query parameters to retrieve only specific attributes and also request a strongly consistent read\. The following C\# code example constructs the request using the `QueryOperationConfig` object\.

**Example**  

```
Table table = Table.LoadTable(client, "Reply");
DateTime twoWeeksAgoDate = DateTime.UtcNow - TimeSpan.FromDays(15);
QueryOperationConfig config = new QueryOperationConfig()
{
  HashKey = "DynamoDB Thread 2", //Partition key
  AttributesToGet = new List<string>
    { "Subject", "ReplyDateTime", "PostedBy" },
  ConsistentRead = true,
  Filter = new RangeFilter(QueryOperator.GreaterThan, twoWeeksAgoDate)
};

Search search = table.Query(config);
```

## Example: Query Using the Table\.Query Method<a name="QueryMidLevelDotNetExampleTableQuery"></a>

The following C\# code example uses the `Table.Query` method to run the following sample queries\.
+ The following queries are run against the `Reply` table\.
  + Find forum thread replies that were posted in the last 15 days\.

    This query is run twice\. In the first `Table.Query` call, the example provides only the required query parameters\. In the second `Table.Query` call, you provide optional query parameters to request a strongly consistent read and a list of attributes to retrieve\.
  + Find forum thread replies posted during a period of time\.

    This query uses the `Between` query operator to find replies posted in between two dates\.
+ Get a product from the `ProductCatalog` table\.

  Because the `ProductCatalog` table has a primary key that is only a partition key, you can only get items; you cannot query the table\. The example retrieves a specific product item using the item `Id`\.



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
using System.Linq;
using Amazon.DynamoDBv2;
using Amazon.DynamoDBv2.DocumentModel;
using Amazon.Runtime;
using Amazon.SecurityToken;

namespace com.amazonaws.codesamples
{
    class MidLevelQueryAndScan
    {
        private static AmazonDynamoDBClient client = new AmazonDynamoDBClient();

        static void Main(string[] args)
        {
            try
            {
                // Query examples.
                Table replyTable = Table.LoadTable(client, "Reply");
                string forumName = "Amazon DynamoDB";
                string threadSubject = "DynamoDB Thread 2";
                FindRepliesInLast15Days(replyTable, forumName, threadSubject);
                FindRepliesInLast15DaysWithConfig(replyTable, forumName, threadSubject);
                FindRepliesPostedWithinTimePeriod(replyTable, forumName, threadSubject);

                // Get Example.
                Table productCatalogTable = Table.LoadTable(client, "ProductCatalog");
                int productId = 101;
                GetProduct(productCatalogTable, productId);

                Console.WriteLine("To continue, press Enter");
                Console.ReadLine();
            }
            catch (AmazonDynamoDBException e) { Console.WriteLine(e.Message); }
            catch (AmazonServiceException e) { Console.WriteLine(e.Message); }
            catch (Exception e) { Console.WriteLine(e.Message); }
        }

        private static void GetProduct(Table tableName, int productId)
        {
            Console.WriteLine("*** Executing GetProduct() ***");
            Document productDocument = tableName.GetItem(productId);
            if (productDocument != null)
            {
                PrintDocument(productDocument);
            }
            else
            {
                Console.WriteLine("Error: product " + productId + " does not exist");
            }
        }

        private static void FindRepliesInLast15Days(Table table, string forumName, string threadSubject)
        {
            string Attribute = forumName + "#" + threadSubject;

            DateTime twoWeeksAgoDate = DateTime.UtcNow - TimeSpan.FromDays(15);
            QueryFilter filter = new QueryFilter("Id", QueryOperator.Equal, partitionKey);
            filter.AddCondition("ReplyDateTime", QueryOperator.GreaterThan, twoWeeksAgoDate);

            // Use Query overloads that takes the minimum required query parameters.
            Search search = table.Query(filter);

            List<Document> documentSet = new List<Document>();
            do
            {
                documentSet = search.GetNextSet();
                Console.WriteLine("\nFindRepliesInLast15Days: printing ............");
                foreach (var document in documentSet)
                    PrintDocument(document);
            } while (!search.IsDone);
        }

        private static void FindRepliesPostedWithinTimePeriod(Table table, string forumName, string threadSubject)
        {
            DateTime startDate = DateTime.UtcNow.Subtract(new TimeSpan(21, 0, 0, 0));
            DateTime endDate = DateTime.UtcNow.Subtract(new TimeSpan(1, 0, 0, 0));

            QueryFilter filter = new QueryFilter("Id", QueryOperator.Equal, forumName + "#" + threadSubject);
            filter.AddCondition("ReplyDateTime", QueryOperator.Between, startDate, endDate);

            QueryOperationConfig config = new QueryOperationConfig()
            {
                Limit = 2, // 2 items/page.
                Select = SelectValues.SpecificAttributes,
                AttributesToGet = new List<string> { "Message",
                                 "ReplyDateTime",
                                 "PostedBy" },
                ConsistentRead = true,
                Filter = filter
            };

            Search search = table.Query(config);

            List<Document> documentList = new List<Document>();

            do
            {
                documentList = search.GetNextSet();
                Console.WriteLine("\nFindRepliesPostedWithinTimePeriod: printing replies posted within dates: {0} and {1} ............", startDate, endDate);
                foreach (var document in documentList)
                {
                    PrintDocument(document);
                }
            } while (!search.IsDone);
        }

        private static void FindRepliesInLast15DaysWithConfig(Table table, string forumName, string threadName)
        {
            DateTime twoWeeksAgoDate = DateTime.UtcNow - TimeSpan.FromDays(15);
            QueryFilter filter = new QueryFilter("Id", QueryOperator.Equal, forumName + "#" + threadName);
            filter.AddCondition("ReplyDateTime", QueryOperator.GreaterThan, twoWeeksAgoDate);
            // You are specifying optional parameters so use QueryOperationConfig.
            QueryOperationConfig config = new QueryOperationConfig()
            {
                Filter = filter,
                // Optional parameters.
                Select = SelectValues.SpecificAttributes,
                AttributesToGet = new List<string> { "Message", "ReplyDateTime",
                                 "PostedBy" },
                ConsistentRead = true
            };

            Search search = table.Query(config);

            List<Document> documentSet = new List<Document>();
            do
            {
                documentSet = search.GetNextSet();
                Console.WriteLine("\nFindRepliesInLast15DaysWithConfig: printing ............");
                foreach (var document in documentSet)
                    PrintDocument(document);
            } while (!search.IsDone);
        }

        private static void PrintDocument(Document document)
        {
            //   count++;
            Console.WriteLine();
            foreach (var attribute in document.GetAttributeNames())
            {
                string stringValue = null;
                var value = document[attribute];
                if (value is Primitive)
                    stringValue = value.AsPrimitive().Value.ToString();
                else if (value is PrimitiveList)
                    stringValue = string.Join(",", (from primitive
                                    in value.AsPrimitiveList().Entries
                                                    select primitive.Value).ToArray());
                Console.WriteLine("{0} - {1}", attribute, stringValue);
            }
        }
    }
}
```