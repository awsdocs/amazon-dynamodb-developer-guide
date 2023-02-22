# Querying tables and indexes: \.NET<a name="LowLevelDotNetQuerying"></a>

The `Query` operation enables you to query a table or a secondary index in Amazon DynamoDB\. You must provide a partition key value and an equality condition\. If the table or index has a sort key, you can refine the results by providing a sort key value and a condition\.

The following are the steps to query a table using the low\-level AWS SDK for \.NET API\.

1. Create an instance of the `AmazonDynamoDBClient` class\.

1. Create an instance of the `QueryRequest` class and provide query operation parameters\.

1. Run the `Query` method and provide the `QueryRequest` object that you created in the preceding step\.

   The response includes the `QueryResult` object that provides all items returned by the query\.

The following C\# code example demonstrates the preceding tasks\. The code assumes that you have a `Reply` table that stores replies for forum threads\. For more information, see [Creating tables and loading data for code examples in DynamoDB](SampleData.md)\.

**Example**  

```
Reply Id, ReplyDateTime, ... )
```

Each forum thread has a unique ID and can have zero or more replies\. Therefore, the primary key is composed of both the `Id` \(partition key\) and `ReplyDateTime` \(sort key\)\. 

The following query retrieves all replies for a specific thread subject\. The query requires both the table name and the `Subject` value\.

**Example**  

```
AmazonDynamoDBClient client = new AmazonDynamoDBClient();

var request = new QueryRequest
{
    TableName = "Reply",
    KeyConditionExpression = "Id = :v_Id",
    ExpressionAttributeValues = new Dictionary<string, AttributeValue> {
        {":v_Id", new AttributeValue { S =  "Amazon DynamoDB#DynamoDB Thread 1" }}}
};

var response = client.Query(request);

foreach (Dictionary<string, AttributeValue> item in response.Items)
{
    // Process the result.
    PrintItem(item);
}
```

## Specifying optional parameters<a name="LowLevelDotNetQueryingOptions"></a>

The `Query` method supports several optional parameters\. For example, you can optionally narrow the query result in the preceding query to return replies in the past two weeks by specifying a condition\. The condition is called a *sort key condition*, because DynamoDB evaluates the query condition that you specify against the sort key of the primary key\. You can specify other optional parameters to retrieve only a specific list of attributes from items in the query result\. For more information, see [Query](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Query.html)\.

The following C\# code example retrieves forum thread replies posted in the past 15 days\. The example specifies the following optional parameters:
+ A `KeyConditionExpression` to retrieve only the replies in the past 15 days\.
+ A `ProjectionExpression` parameter to specify a list of attributes to retrieve for items in the query result\.
+ A `ConsistentRead` parameter to perform a strongly consistent read\.

**Example**  

```
DateTime twoWeeksAgoDate = DateTime.UtcNow - TimeSpan.FromDays(15);
string twoWeeksAgoString = twoWeeksAgoDate.ToString(AWSSDKUtils.ISO8601DateFormat);

var request = new QueryRequest
{
    TableName = "Reply",
    KeyConditionExpression = "Id = :v_Id and ReplyDateTime > :v_twoWeeksAgo",
    ExpressionAttributeValues = new Dictionary<string, AttributeValue> {
        {":v_Id", new AttributeValue { S =  "Amazon DynamoDB#DynamoDB Thread 2" }},
        {":v_twoWeeksAgo", new AttributeValue { S =  twoWeeksAgoString }}
    },
    ProjectionExpression = "Subject, ReplyDateTime, PostedBy",
    ConsistentRead = true
};

var response = client.Query(request);

foreach (Dictionary<string, AttributeValue> item in response.Items)
{
    // Process the result.
    PrintItem(item);
}
```

You can also optionally limit the page size, or the number of items per page, by adding the optional `Limit` parameter\. Each time you run the `Query` method, you get one page of results that has the specified number of items\. To fetch the next page, you run the `Query` method again by providing the primary key value of the last item in the previous page so that the method can return the next set of items\. You provide this information in the request by setting the `ExclusiveStartKey` property\. Initially, this property can be null\. To retrieve subsequent pages, you must update this property value to the primary key of the last item in the preceding page\.

The following C\# example queries the `Reply` table\. In the request, it specifies the `Limit` and `ExclusiveStartKey` optional parameters\. The `do/while` loop continues to scan one page at time until the `LastEvaluatedKey` returns a null value\.

**Example**  

```
Dictionary<string,AttributeValue> lastKeyEvaluated = null;

do
{
    var request = new QueryRequest
    {
        TableName = "Reply",
        KeyConditionExpression = "Id = :v_Id",
        ExpressionAttributeValues = new Dictionary<string, AttributeValue> {
            {":v_Id", new AttributeValue { S =  "Amazon DynamoDB#DynamoDB Thread 2" }}
        },

        // Optional parameters.
        Limit = 1,
        ExclusiveStartKey = lastKeyEvaluated
    };

    var response = client.Query(request);

    // Process the query result.
    foreach (Dictionary<string, AttributeValue> item in response.Items)
    {
         PrintItem(item);
    }

    lastKeyEvaluated = response.LastEvaluatedKey;

} while (lastKeyEvaluated != null && lastKeyEvaluated.Count != 0);
```

## Example \- querying using the AWS SDK for \.NET<a name="LowLevelDotNetQueryExample"></a>

The following tables store information about a collection of forums\. For more information, see [Creating tables and loading data for code examples in DynamoDB](SampleData.md)\.



**Example**  

```
Forum ( Name, ... )
Thread ( ForumName, Subject, Message, LastPostedBy, LastPostDateTime, ...)
Reply ( Id, ReplyDateTime, Message, PostedBy, ...)
```

In this example, you run variations of "Find replies for a thread "DynamoDB Thread 1" in forum "DynamoDB"\.
+ Find replies for a thread\.
+ Find replies for a thread\. Specify the `Limit` query parameter to set page size\. 

  This function illustrates the use of pagination to process multipage result\. DynamoDB has a page size limit and if your result exceeds the page size, you get only the first page of results\. This coding pattern ensures your code processes all the pages in the query result\.
+ Find replies in the last 15 days\.
+ Find replies in a specific date range\. 

  The preceding two queries show how you can specify sort key conditions to narrow query results and use other optional query parameters\. 

For step\-by\-step instructions for testing the following example, see [\.NET code examples](CodeSamples.DotNet.md)\.

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
using Amazon.DynamoDBv2.Model;
using Amazon.Runtime;
using Amazon.Util;

namespace com.amazonaws.codesamples
{
    class LowLevelQuery
    {
        private static AmazonDynamoDBClient client = new AmazonDynamoDBClient();

        static void Main(string[] args)
        {
            try
            {
                // Query a specific forum and thread.
                string forumName = "Amazon DynamoDB";
                string threadSubject = "DynamoDB Thread 1";

                FindRepliesForAThread(forumName, threadSubject);
                FindRepliesForAThreadSpecifyOptionalLimit(forumName, threadSubject);
                FindRepliesInLast15DaysWithConfig(forumName, threadSubject);
                FindRepliesPostedWithinTimePeriod(forumName, threadSubject);

                Console.WriteLine("Example complete. To continue, press Enter");
                Console.ReadLine();
            }
            catch (AmazonDynamoDBException e) { Console.WriteLine(e.Message); Console.ReadLine(); }
            catch (AmazonServiceException e) { Console.WriteLine(e.Message); Console.ReadLine(); }
            catch (Exception e) { Console.WriteLine(e.Message); Console.ReadLine(); }
        }

        private static void FindRepliesPostedWithinTimePeriod(string forumName, string threadSubject)
        {
            Console.WriteLine("*** Executing FindRepliesPostedWithinTimePeriod() ***");
            string replyId = forumName + "#" + threadSubject;
            // You must provide date value based on your test data.
            DateTime startDate = DateTime.UtcNow - TimeSpan.FromDays(21);
            string start = startDate.ToString(AWSSDKUtils.ISO8601DateFormat);

            // You provide date value based on your test data.
            DateTime endDate = DateTime.UtcNow - TimeSpan.FromDays(5);
            string end = endDate.ToString(AWSSDKUtils.ISO8601DateFormat);

            var request = new QueryRequest
            {
                TableName = "Reply",
                ReturnConsumedCapacity = "TOTAL",
                KeyConditionExpression = "Id = :v_replyId and ReplyDateTime between :v_start and :v_end",
                ExpressionAttributeValues = new Dictionary<string, AttributeValue> {
                {":v_replyId", new AttributeValue {
                     S = replyId
                 }},
                {":v_start", new AttributeValue {
                     S = start
                 }},
                {":v_end", new AttributeValue {
                     S = end
                 }}
            }
            };

            var response = client.Query(request);

            Console.WriteLine("\nNo. of reads used (by query in FindRepliesPostedWithinTimePeriod) {0}",
                      response.ConsumedCapacity.CapacityUnits);
            foreach (Dictionary<string, AttributeValue> item
                 in response.Items)
            {
                PrintItem(item);
            }
            Console.WriteLine("To continue, press Enter");
            Console.ReadLine();
        }

        private static void FindRepliesInLast15DaysWithConfig(string forumName, string threadSubject)
        {
            Console.WriteLine("*** Executing FindRepliesInLast15DaysWithConfig() ***");
            string replyId = forumName + "#" + threadSubject;

            DateTime twoWeeksAgoDate = DateTime.UtcNow - TimeSpan.FromDays(15);
            string twoWeeksAgoString =
                twoWeeksAgoDate.ToString(AWSSDKUtils.ISO8601DateFormat);

            var request = new QueryRequest
            {
                TableName = "Reply",
                ReturnConsumedCapacity = "TOTAL",
                KeyConditionExpression = "Id = :v_replyId and ReplyDateTime > :v_interval",
                ExpressionAttributeValues = new Dictionary<string, AttributeValue> {
                {":v_replyId", new AttributeValue {
                     S = replyId
                 }},
                {":v_interval", new AttributeValue {
                     S = twoWeeksAgoString
                 }}
            },

                // Optional parameter.
                ProjectionExpression = "Id, ReplyDateTime, PostedBy",
                // Optional parameter.
                ConsistentRead = true
            };

            var response = client.Query(request);

            Console.WriteLine("No. of reads used (by query in FindRepliesInLast15DaysWithConfig) {0}",
                      response.ConsumedCapacity.CapacityUnits);
            foreach (Dictionary<string, AttributeValue> item
                 in response.Items)
            {
                PrintItem(item);
            }
            Console.WriteLine("To continue, press Enter");
            Console.ReadLine();
        }

        private static void FindRepliesForAThreadSpecifyOptionalLimit(string forumName, string threadSubject)
        {
            Console.WriteLine("*** Executing FindRepliesForAThreadSpecifyOptionalLimit() ***");
            string replyId = forumName + "#" + threadSubject;

            Dictionary<string, AttributeValue> lastKeyEvaluated = null;
            do
            {
                var request = new QueryRequest
                {
                    TableName = "Reply",
                    ReturnConsumedCapacity = "TOTAL",
                    KeyConditionExpression = "Id = :v_replyId",
                    ExpressionAttributeValues = new Dictionary<string, AttributeValue> {
                    {":v_replyId", new AttributeValue {
                         S = replyId
                     }}
                },
                    Limit = 2, // The Reply table has only a few sample items. So the page size is smaller.
                    ExclusiveStartKey = lastKeyEvaluated
                };

                var response = client.Query(request);

                Console.WriteLine("No. of reads used (by query in FindRepliesForAThreadSpecifyLimit) {0}\n",
                          response.ConsumedCapacity.CapacityUnits);
                foreach (Dictionary<string, AttributeValue> item
                     in response.Items)
                {
                    PrintItem(item);
                }
                lastKeyEvaluated = response.LastEvaluatedKey;
            } while (lastKeyEvaluated != null && lastKeyEvaluated.Count != 0);

            Console.WriteLine("To continue, press Enter");


            Console.ReadLine();
        }

        private static void FindRepliesForAThread(string forumName, string threadSubject)
        {
            Console.WriteLine("*** Executing FindRepliesForAThread() ***");
            string replyId = forumName + "#" + threadSubject;

            var request = new QueryRequest
            {
                TableName = "Reply",
                ReturnConsumedCapacity = "TOTAL",
                KeyConditionExpression = "Id = :v_replyId",
                ExpressionAttributeValues = new Dictionary<string, AttributeValue> {
                {":v_replyId", new AttributeValue {
                     S = replyId
                 }}
            }
            };

            var response = client.Query(request);
            Console.WriteLine("No. of reads used (by query in FindRepliesForAThread) {0}\n",
                      response.ConsumedCapacity.CapacityUnits);
            foreach (Dictionary<string, AttributeValue> item in response.Items)
            {
                PrintItem(item);
            }
            Console.WriteLine("To continue, press Enter");
            Console.ReadLine();
        }

        private static void PrintItem(
            Dictionary<string, AttributeValue> attributeList)
        {
            foreach (KeyValuePair<string, AttributeValue> kvp in attributeList)
            {
                string attributeName = kvp.Key;
                AttributeValue value = kvp.Value;

                Console.WriteLine(
                    attributeName + " " +
                    (value.S == null ? "" : "S=[" + value.S + "]") +
                    (value.N == null ? "" : "N=[" + value.N + "]") +
                    (value.SS == null ? "" : "SS=[" + string.Join(",", value.SS.ToArray()) + "]") +
                    (value.NS == null ? "" : "NS=[" + string.Join(",", value.NS.ToArray()) + "]")
                    );
            }
            Console.WriteLine("************************************************");
        }
    }
}
```