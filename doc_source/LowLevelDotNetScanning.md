# Scanning tables and indexes: \.NET<a name="LowLevelDotNetScanning"></a>



The `Scan` operation reads all of the items in a table or index in Amazon DynamoDB\.

The following are the steps to scan a table using the AWS SDK for \.NET low\-level API:

1. Create an instance of the `AmazonDynamoDBClient` class\.

1. Create an instance of the `ScanRequest` class and provide scan operation parameters\.

   The only required parameter is the table name\.

1. Run the `Scan` method and provide the `ScanRequest` object that you created in the preceding step\.

The following `Reply` table stores replies for forum threads\.

**Example**  

```
>Reply ( <emphasis role="underline">Id</emphasis>, <emphasis role="underline">ReplyDateTime</emphasis>, Message, PostedBy )
```

The table maintains all the replies for various forum threads\. Therefore, the primary key is composed of both the `Id` \(partition key\) and `ReplyDateTime` \(sort key\)\. The following C\# code example scans the entire table\. The `ScanRequest` instance specifies the name of the table to scan\.

**Example**  

```
AmazonDynamoDBClient client = new AmazonDynamoDBClient();

var request = new ScanRequest
{
    TableName = "Reply",
};

var response = client.Scan(request);
var result = response.ScanResult;

foreach (Dictionary<string, AttributeValue> item in response.ScanResult.Items)
{
  // Process the result.
  PrintItem(item);
}
```

## Specifying optional parameters<a name="LowLevelDotNetScanningOptions"></a>



The `Scan` method supports several optional parameters\. For example, you can optionally use a scan filter to filter the scan result\. In a scan filter, you can specify a condition and an attribute name on which you want the condition evaluated\. For more information, see [Scan](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Scan.html)\.

The following C\# code scans the `ProductCatalog` table to find items that are priced less than 0\. The sample specifies the following optional parameters:
+ A `FilterExpression` parameter to retrieve only the items priced less than 0 \(error condition\)\.
+ A `ProjectionExpression` parameter to specify the attributes to retrieve for items in the query results\.

The following C\# example scans the `ProductCatalog` table to find all items priced less than 0\.

**Example**  

```
var forumScanRequest = new ScanRequest
 {
   TableName = "ProductCatalog",
   // Optional parameters.
   ExpressionAttributeValues = new Dictionary<string,AttributeValue> {
        {":val", new AttributeValue { N = "0" }}
   },
   FilterExpression = "Price < :val",
   ProjectionExpression = "Id"
 };
```

You can also optionally limit the page size or the number of items per page, by adding the optional `Limit` parameter\. Each time you run the `Scan` method, you get one page of results that has the specified number of items\. To fetch the next page, you run the `Scan` method again by providing the primary key value of the last item in the previous page so that the `Scan` method can return the next set of items\. You provide this information in the request by setting the `ExclusiveStartKey` property\. Initially, this property can be null\. To retrieve subsequent pages, you must update this property value to the primary key of the last item in the preceding page\.

The following C\# code example scans the `ProductCatalog` table\. In the request, it specifies the `Limit` and `ExclusiveStartKey` optional parameters\. The `do/while` loop continues to scan one page at time until the `LastEvaluatedKey` returns a null value\.

**Example**  

```
Dictionary<string, AttributeValue> lastKeyEvaluated = null;
do
{
    var request = new ScanRequest
    {
        TableName = "ProductCatalog",
        Limit = 10,
        ExclusiveStartKey = lastKeyEvaluated
    };

    var response = client.Scan(request);

    foreach (Dictionary<string, AttributeValue> item
      in response.Items)
    {
        PrintItem(item);
    }
    lastKeyEvaluated = response.LastEvaluatedKey;

} while (lastKeyEvaluated != null && lastKeyEvaluated.Count != 0);
```

## Example \- scan using \.NET<a name="LowLevelDotNetScanExample"></a>

The following C\# code provides a working example that scans the `ProductCatalog` table to find items priced less than 0\.

For step\-by\-step instructions for testing the following sample, see [\.NET code examples](CodeSamples.DotNet.md)\.

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

namespace com.amazonaws.codesamples
{
    class LowLevelScan
    {
        private static AmazonDynamoDBClient client = new AmazonDynamoDBClient();

        static void Main(string[] args)
        {
            try
            {
                FindProductsForPriceLessThanZero();

                Console.WriteLine("Example complete. To continue, press Enter");
                Console.ReadLine();
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
                Console.WriteLine("To continue, press Enter");
                Console.ReadLine();
            }
        }

        private static void FindProductsForPriceLessThanZero()
        {
            Dictionary<string, AttributeValue> lastKeyEvaluated = null;
            do
            {
                var request = new ScanRequest
                {
                    TableName = "ProductCatalog",
                    Limit = 2,
                    ExclusiveStartKey = lastKeyEvaluated,
                    ExpressionAttributeValues = new Dictionary<string, AttributeValue> {
                    {":val", new AttributeValue {
                         N = "0"
                     }}
                },
                    FilterExpression = "Price < :val",

                    ProjectionExpression = "Id, Title, Price"
                };

                var response = client.Scan(request);

                foreach (Dictionary<string, AttributeValue> item
                     in response.Items)
                {
                    Console.WriteLine("\nScanThreadTableUsePaging - printing.....");
                    PrintItem(item);
                }
                lastKeyEvaluated = response.LastEvaluatedKey;
            } while (lastKeyEvaluated != null && lastKeyEvaluated.Count != 0);

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

## Example \- parallel scan using \.NET<a name="LowLevelDotNetParallelScanExample"></a>

The following C\# code example demonstrates a parallel scan\. The program deletes and then re\-creates the `ProductCatalog` table and then loads the table with data\. When the data load is finished, the program spawns multiple threads and issues parallel `Scan` requests\. Finally, the program prints a summary of runtime statistics\.

For step\-by\-step instructions for testing the following sample, see [\.NET code examples](CodeSamples.DotNet.md)\.

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
using System.Threading;
using System.Threading.Tasks;
using Amazon.DynamoDBv2;
using Amazon.DynamoDBv2.Model;
using Amazon.Runtime;

namespace com.amazonaws.codesamples
{
    class LowLevelParallelScan
    {
        private static AmazonDynamoDBClient client = new AmazonDynamoDBClient();
        private static string tableName = "ProductCatalog";
        private static int exampleItemCount = 100;
        private static int scanItemLimit = 10;
        private static int totalSegments = 5;


        static void Main(string[] args)
        {
            try
            {
                DeleteExampleTable();
                CreateExampleTable();
                UploadExampleData();
                ParallelScanExampleTable();
            }
            catch (AmazonDynamoDBException e) { Console.WriteLine(e.Message); }
            catch (AmazonServiceException e) { Console.WriteLine(e.Message); }
            catch (Exception e) { Console.WriteLine(e.Message); }

            Console.WriteLine("To continue, press Enter");
            Console.ReadLine();
        }

        private static void ParallelScanExampleTable()
        {
            Console.WriteLine("\n*** Creating {0} Parallel Scan Tasks to scan {1}", totalSegments, tableName);
            Task[] tasks = new Task[totalSegments];
            for (int segment = 0; segment < totalSegments; segment++)
            {
                int tmpSegment = segment;
                Task task = Task.Factory.StartNew(() =>
                                  {
                                      ScanSegment(totalSegments, tmpSegment);
                                  });

                tasks[segment] = task;
            }

            Console.WriteLine("All scan tasks are created, waiting for them to complete.");
            Task.WaitAll(tasks);

            Console.WriteLine("All scan tasks are completed.");
        }

        private static void ScanSegment(int totalSegments, int segment)
        {
            Console.WriteLine("*** Starting to Scan Segment {0} of {1} out of {2} total segments ***", segment, tableName, totalSegments);
            Dictionary<string, AttributeValue> lastEvaluatedKey = null;
            int totalScannedItemCount = 0;
            int totalScanRequestCount = 0;
            do
            {
                var request = new ScanRequest
                {
                    TableName = tableName,
                    Limit = scanItemLimit,
                    ExclusiveStartKey = lastEvaluatedKey,
                    Segment = segment,
                    TotalSegments = totalSegments
                };

                var response = client.Scan(request);
                lastEvaluatedKey = response.LastEvaluatedKey;
                totalScanRequestCount++;
                totalScannedItemCount += response.ScannedCount;
                foreach (var item in response.Items)
                {
                    Console.WriteLine("Segment: {0}, Scanned Item with Title: {1}", segment, item["Title"].S);
                }
            } while (lastEvaluatedKey.Count != 0);

            Console.WriteLine("*** Completed Scan Segment {0} of {1}. TotalScanRequestCount: {2}, TotalScannedItemCount: {3} ***", segment, tableName, totalScanRequestCount, totalScannedItemCount);
        }

        private static void UploadExampleData()
        {
            Console.WriteLine("\n*** Uploading {0} Example Items to {1} Table***", exampleItemCount, tableName);
            Console.Write("Uploading Items: ");
            for (int itemIndex = 0; itemIndex < exampleItemCount; itemIndex++)
            {
                Console.Write("{0}, ", itemIndex);
                CreateItem(itemIndex.ToString());
            }
            Console.WriteLine();
        }

        private static void CreateItem(string itemIndex)
        {
            var request = new PutItemRequest
            {
                TableName = tableName,
                Item = new Dictionary<string, AttributeValue>()
            {
                { "Id", new AttributeValue {
                      N = itemIndex
                  }},
                { "Title", new AttributeValue {
                      S = "Book " + itemIndex + " Title"
                  }},
                { "ISBN", new AttributeValue {
                      S = "11-11-11-11"
                  }},
                { "Authors", new AttributeValue {
                      SS = new List<string>{"Author1", "Author2" }
                  }},
                { "Price", new AttributeValue {
                      N = "20.00"
                  }},
                { "Dimensions", new AttributeValue {
                      S = "8.5x11.0x.75"
                  }},
                { "InPublication", new AttributeValue {
                      BOOL = false
                  } }
            }
            };
            client.PutItem(request);
        }

        private static void CreateExampleTable()
        {
            Console.WriteLine("\n*** Creating {0} Table ***", tableName);
            var request = new CreateTableRequest
            {
                AttributeDefinitions = new List<AttributeDefinition>()
            {
                new AttributeDefinition
                {
                    AttributeName = "Id",
                    AttributeType = "N"
                }
            },
                KeySchema = new List<KeySchemaElement>
            {
                new KeySchemaElement
                {
                    AttributeName = "Id",
                    KeyType = "HASH" //Partition key
                }
            },
                ProvisionedThroughput = new ProvisionedThroughput
                {
                    ReadCapacityUnits = 5,
                    WriteCapacityUnits = 6
                },
                TableName = tableName
            };

            var response = client.CreateTable(request);

            var result = response;
            var tableDescription = result.TableDescription;
            Console.WriteLine("{1}: {0} \t ReadsPerSec: {2} \t WritesPerSec: {3}",
                      tableDescription.TableStatus,
                      tableDescription.TableName,
                      tableDescription.ProvisionedThroughput.ReadCapacityUnits,
                      tableDescription.ProvisionedThroughput.WriteCapacityUnits);

            string status = tableDescription.TableStatus;
            Console.WriteLine(tableName + " - " + status);

            WaitUntilTableReady(tableName);
        }

        private static void DeleteExampleTable()
        {
            try
            {
                Console.WriteLine("\n*** Deleting {0} Table ***", tableName);
                var request = new DeleteTableRequest
                {
                    TableName = tableName
                };

                var response = client.DeleteTable(request);
                var result = response;
                Console.WriteLine("{0} is being deleted...", tableName);
                WaitUntilTableDeleted(tableName);
            }
            catch (ResourceNotFoundException)
            {
                Console.WriteLine("{0} Table delete failed: Table does not exist", tableName);
            }
        }

        private static void WaitUntilTableReady(string tableName)
        {
            string status = null;
            // Let us wait until table is created. Call DescribeTable.
            do
            {
                System.Threading.Thread.Sleep(5000); // Wait 5 seconds.
                try
                {
                    var res = client.DescribeTable(new DescribeTableRequest
                    {
                        TableName = tableName
                    });

                    Console.WriteLine("Table name: {0}, status: {1}",
                              res.Table.TableName,
                              res.Table.TableStatus);
                    status = res.Table.TableStatus;
                }
                catch (ResourceNotFoundException)
                {
                    // DescribeTable is eventually consistent. So you might
                    // get resource not found. So we handle the potential exception.
                }
            } while (status != "ACTIVE");
        }

        private static void WaitUntilTableDeleted(string tableName)
        {
            string status = null;
            // Let us wait until table is deleted. Call DescribeTable.
            do
            {
                System.Threading.Thread.Sleep(5000); // Wait 5 seconds.
                try
                {
                    var res = client.DescribeTable(new DescribeTableRequest
                    {
                        TableName = tableName
                    });

                    Console.WriteLine("Table name: {0}, status: {1}",
                              res.Table.TableName,
                              res.Table.TableStatus);
                    status = res.Table.TableStatus;
                }
                catch (ResourceNotFoundException)
                {
                    Console.WriteLine("Table name: {0} is not found. It is deleted", tableName);
                    return;
                }
            } while (status == "DELETING");
        }
    }
}
```