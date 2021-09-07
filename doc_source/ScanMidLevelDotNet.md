# Table\.Scan Method in the AWS SDK for \.NET<a name="ScanMidLevelDotNet"></a>

The `Scan` method performs a full table scan\. It provides two overloads\. The only parameter required by the `Scan` method is the scan filter, which you can provide using the following overload\.

**Example**  

```
Scan(ScanFilter filter);
```

For example, assume that you maintain a table of forum threads tracking information such as thread subject \(primary\), the related message, forum `Id` to which the thread belongs, `Tags`, and other information\. Assume that the subject is the primary key\.

**Example**  

```
Thread(Subject, Message, ForumId, Tags, LastPostedDateTime, .... )
```

This is a simplified version of forums and threads that you see on AWS forums \(see [Discussion Forums](https://forums.aws.amazon.com/)\)\. The following C\# code example queries all threads in a specific forum \(ForumId = 101\) that are tagged "sortkey"\. Because the `ForumId` is not a primary key, the example scans the table\. The `ScanFilter` includes two conditions\. The query returns all the threads that satisfy both of the conditions\.

**Example**  

```
string tableName = "Thread";
Table ThreadTable = Table.LoadTable(client, tableName);

ScanFilter scanFilter = new ScanFilter();
scanFilter.AddCondition("ForumId", ScanOperator.Equal, 101);
scanFilter.AddCondition("Tags", ScanOperator.Contains, "sortkey");

Search search = ThreadTable.Scan(scanFilter);
```

## Specifying Optional Parameters<a name="ScanMidLevelDotNetOptions"></a>

You also can specify optional parameters to `Scan`, such as a specific list of attributes to retrieve or whether to perform a strongly consistent read\. To specify optional parameters, you must create a `ScanOperationConfig` object that includes both the required and optional parameters and use the following overload\. 

**Example**  

```
Scan(ScanOperationConfig config);
```

The following C\# code example runs the same preceding query \(find forum threads in which the `ForumId` is `101` and the `Tag` attribute contains the "sortkey" keyword\)\. Assume that you want to add an optional parameter to retrieve only a specific attribute list\. In this case, you must create a `ScanOperationConfig` object by providing all the parameters, required and optional parameters, as shown in the following code example\.

**Example**  

```
string tableName = "Thread";
Table ThreadTable = Table.LoadTable(client, tableName);

ScanFilter scanFilter = new ScanFilter();
scanFilter.AddCondition("ForumId", ScanOperator.Equal, forumId);
scanFilter.AddCondition("Tags", ScanOperator.Contains, "sortkey");

ScanOperationConfig config = new ScanOperationConfig()
{
  AttributesToGet = new List<string> { "Subject", "Message" } ,
  Filter = scanFilter
};

Search search = ThreadTable.Scan(config);
```

## Example: Scan Using the Table\.Scan Method<a name="ScanMidLevelDotNetExampleTableScan"></a>

The `Scan` operation performs a full table scan making it a potentially expensive operation\. You should use queries instead\. However, there are times when you might need to run a scan against a table\. For example, you might have a data entry error in the product pricing, and you must scan the table as shown in the following C\# code example\. The example scans the `ProductCatalog` table to find products for which the price value is less than 0\. The example illustrates the use of the two `Table.Scan` overloads\. 
+ `Table.Scan` that takes the `ScanFilter` object as a parameter\. 

  You can pass the `ScanFilter` parameter when passing in only the required parameters\.
+ `Table.Scan` that takes the `ScanOperationConfig` object as a parameter\. 

  You must use the `ScanOperationConfig` parameter if you want to pass any optional parameters to the `Scan` method\. 

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

namespace com.amazonaws.codesamples
{
    class MidLevelScanOnly
    {
        private static AmazonDynamoDBClient client = new AmazonDynamoDBClient();

        static void Main(string[] args)
        {
            Table productCatalogTable = Table.LoadTable(client, "ProductCatalog");
            // Scan example.
            FindProductsWithNegativePrice(productCatalogTable);
            FindProductsWithNegativePriceWithConfig(productCatalogTable);

            Console.WriteLine("To continue, press Enter");
            Console.ReadLine();
        }

        private static void FindProductsWithNegativePrice(Table productCatalogTable)
        {
            // Assume there is a price error. So we scan to find items priced < 0.
            ScanFilter scanFilter = new ScanFilter();
            scanFilter.AddCondition("Price", ScanOperator.LessThan, 0);

            Search search = productCatalogTable.Scan(scanFilter);

            List<Document> documentList = new List<Document>();
            do
            {
                documentList = search.GetNextSet();
                Console.WriteLine("\nFindProductsWithNegativePrice: printing ............");
                foreach (var document in documentList)
                    PrintDocument(document);
            } while (!search.IsDone);
        }

        private static void FindProductsWithNegativePriceWithConfig(Table productCatalogTable)
        {
            // Assume there is a price error. So we scan to find items priced < 0.
            ScanFilter scanFilter = new ScanFilter();
            scanFilter.AddCondition("Price", ScanOperator.LessThan, 0);

            ScanOperationConfig config = new ScanOperationConfig()
            {
                Filter = scanFilter,
                Select = SelectValues.SpecificAttributes,
                AttributesToGet = new List<string> { "Title", "Id" }
            };

            Search search = productCatalogTable.Scan(config);

            List<Document> documentList = new List<Document>();
            do
            {
                documentList = search.GetNextSet();
                Console.WriteLine("\nFindProductsWithNegativePriceWithConfig: printing ............");
                foreach (var document in documentList)
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