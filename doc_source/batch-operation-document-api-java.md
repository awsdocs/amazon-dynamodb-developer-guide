# Example: Batch operations using AWS SDK for Java document API<a name="batch-operation-document-api-java"></a>

This section provides examples of batch write and batch get operations in Amazon DynamoDB using the AWS SDK for Java Document API\.

**Note**  
The SDK for Java also provides an object persistence model, enabling you to map your client\-side classes to DynamoDB tables\. This approach can reduce the amount of code that you have to write\. For more information, see [Java: DynamoDBMapper](DynamoDBMapper.md)\.

**Topics**
+ [Example: Batch write operation using the AWS SDK for Java document API](#JavaDocumentAPIBatchWrite)
+ [Example: Batch get operation using the AWS SDK for Java document API](#JavaDocumentAPIBatchGet)

## Example: Batch write operation using the AWS SDK for Java document API<a name="JavaDocumentAPIBatchWrite"></a>

The following Java code example uses the `batchWriteItem` method to perform the following put and delete operations:
+ Put one item in the `Forum` table\.
+ Put one item and delete one item from the `Thread` table\. 

You can specify any number of put and delete requests against one or more tables when creating your batch write request\. However, `batchWriteItem` limits the size of a batch write request and the number of put and delete operations in a single batch write operation\. If your request exceeds these limits, your request is rejected\. If your table does not have sufficient provisioned throughput to serve this request, the unprocessed request items are returned in the response\. 

The following example checks the response to see if it has any unprocessed request items\. If it does, it loops back and resends the `batchWriteItem` request with unprocessed items in the request\. If you followed the [Creating tables and loading data for code examples in DynamoDB](SampleData.md) section, you should already have created the `Forum` and `Thread` tables\. You can also create these tables and upload sample data programmatically\. For more information, see [Creating example tables and uploading data using the AWS SDK for Java](AppendixSampleDataCodeJava.md)\.

For step\-by\-step instructions for testing the following sample, see [Java code examples](CodeSamples.Java.md)\. 

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


package com.amazonaws.codesamples.document;

import java.io.IOException;
import java.util.Arrays;
import java.util.HashSet;
import java.util.List;
import java.util.Map;

import com.amazonaws.services.dynamodbv2.AmazonDynamoDB;
import com.amazonaws.services.dynamodbv2.AmazonDynamoDBClientBuilder;
import com.amazonaws.services.dynamodbv2.document.BatchWriteItemOutcome;
import com.amazonaws.services.dynamodbv2.document.DynamoDB;
import com.amazonaws.services.dynamodbv2.document.Item;
import com.amazonaws.services.dynamodbv2.document.TableWriteItems;
import com.amazonaws.services.dynamodbv2.model.WriteRequest;

public class DocumentAPIBatchWrite {

    static AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard().build();
    static DynamoDB dynamoDB = new DynamoDB(client);

    static String forumTableName = "Forum";
    static String threadTableName = "Thread";

    public static void main(String[] args) throws IOException {

        writeMultipleItemsBatchWrite();

    }

    private static void writeMultipleItemsBatchWrite() {
        try {

            // Add a new item to Forum
            TableWriteItems forumTableWriteItems = new TableWriteItems(forumTableName) // Forum
                .withItemsToPut(new Item().withPrimaryKey("Name", "Amazon RDS").withNumber("Threads", 0));

            // Add a new item, and delete an existing item, from Thread
            // This table has a partition key and range key, so need to specify
            // both of them
            TableWriteItems threadTableWriteItems = new TableWriteItems(threadTableName)
                .withItemsToPut(new Item().withPrimaryKey("ForumName", "Amazon RDS", "Subject", "Amazon RDS Thread 1")
                    .withString("Message", "ElastiCache Thread 1 message")
                    .withStringSet("Tags", new HashSet<String>(Arrays.asList("cache", "in-memory"))))
                .withHashAndRangeKeysToDelete("ForumName", "Subject", "Amazon S3", "S3 Thread 100");

            System.out.println("Making the request.");
            BatchWriteItemOutcome outcome = dynamoDB.batchWriteItem(forumTableWriteItems, threadTableWriteItems);

            do {

                // Check for unprocessed keys which could happen if you exceed
                // provisioned throughput

                Map<String, List<WriteRequest>> unprocessedItems = outcome.getUnprocessedItems();

                if (outcome.getUnprocessedItems().size() == 0) {
                    System.out.println("No unprocessed items found");
                }
                else {
                    System.out.println("Retrieving the unprocessed items");
                    outcome = dynamoDB.batchWriteItemUnprocessed(unprocessedItems);
                }

            } while (outcome.getUnprocessedItems().size() > 0);

        }
        catch (Exception e) {
            System.err.println("Failed to retrieve items: ");
            e.printStackTrace(System.err);
        }

    }

}
```

## Example: Batch get operation using the AWS SDK for Java document API<a name="JavaDocumentAPIBatchGet"></a>

The following Java code example uses the `batchGetItem` method to retrieve multiple items from the `Forum` and the `Thread` tables\. The `BatchGetItemRequest` specifies the table names and a list of keys for each item to get\. The example processes the response by printing the items retrieved\.

**Note**  
This code example assumes that you have already loaded data into DynamoDB for your account by following the instructions in the [Creating tables and loading data for code examples in DynamoDB](SampleData.md) section\.  
For step\-by\-step instructions to run the following example, see [Java code examples](CodeSamples.Java.md)\.

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


package com.amazonaws.codesamples.document;

import java.io.IOException;
import java.util.List;
import java.util.Map;

import com.amazonaws.services.dynamodbv2.AmazonDynamoDB;
import com.amazonaws.services.dynamodbv2.AmazonDynamoDBClientBuilder;
import com.amazonaws.services.dynamodbv2.document.BatchGetItemOutcome;
import com.amazonaws.services.dynamodbv2.document.DynamoDB;
import com.amazonaws.services.dynamodbv2.document.Item;
import com.amazonaws.services.dynamodbv2.document.TableKeysAndAttributes;
import com.amazonaws.services.dynamodbv2.model.KeysAndAttributes;

public class DocumentAPIBatchGet {
    static AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard().build();
    static DynamoDB dynamoDB = new DynamoDB(client);

    static String forumTableName = "Forum";
    static String threadTableName = "Thread";

    public static void main(String[] args) throws IOException {
        retrieveMultipleItemsBatchGet();
    }

    private static void retrieveMultipleItemsBatchGet() {

        try {

            TableKeysAndAttributes forumTableKeysAndAttributes = new TableKeysAndAttributes(forumTableName);
            // Add a partition key
            forumTableKeysAndAttributes.addHashOnlyPrimaryKeys("Name", "Amazon S3", "Amazon DynamoDB");

            TableKeysAndAttributes threadTableKeysAndAttributes = new TableKeysAndAttributes(threadTableName);
            // Add a partition key and a sort key
            threadTableKeysAndAttributes.addHashAndRangePrimaryKeys("ForumName", "Subject", "Amazon DynamoDB",
                "DynamoDB Thread 1", "Amazon DynamoDB", "DynamoDB Thread 2", "Amazon S3", "S3 Thread 1");

            System.out.println("Making the request.");

            BatchGetItemOutcome outcome = dynamoDB.batchGetItem(forumTableKeysAndAttributes,
                threadTableKeysAndAttributes);

            Map<String, KeysAndAttributes> unprocessed = null;

            do {
                for (String tableName : outcome.getTableItems().keySet()) {
                    System.out.println("Items in table " + tableName);
                    List<Item> items = outcome.getTableItems().get(tableName);
                    for (Item item : items) {
                        System.out.println(item.toJSONPretty());
                    }
                }

                // Check for unprocessed keys which could happen if you exceed
                // provisioned
                // throughput or reach the limit on response size.
                unprocessed = outcome.getUnprocessedKeys();

                if (unprocessed.isEmpty()) {
                    System.out.println("No unprocessed keys found");
                }
                else {
                    System.out.println("Retrieving the unprocessed keys");
                    outcome = dynamoDB.batchGetItemUnprocessed(unprocessed);
                }

            } while (!unprocessed.isEmpty());

        }
        catch (Exception e) {
            System.err.println("Failed to retrieve items.");
            System.err.println(e.getMessage());
        }

    }

}
```