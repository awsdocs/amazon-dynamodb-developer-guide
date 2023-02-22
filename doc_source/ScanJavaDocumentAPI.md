# Scanning tables and indexes: Java<a name="ScanJavaDocumentAPI"></a>



The `Scan` operation reads all of the items in a table or index in Amazon DynamoDB\.

The following are the steps to scan a table using the AWS SDK for Java Document API\. 

1. Create an instance of the `AmazonDynamoDB` class\.

1. Create an instance of the `ScanRequest` class and provide scan parameter\.

   The only required parameter is the table name\.

1. Run the `scan` method and provide the `ScanRequest` object that you created in the preceding step\.

The following `Reply` table stores replies for forum threads\. 

**Example**  

```
Reply ( Id, ReplyDateTime, Message, PostedBy )
```

The table maintains all the replies for various forum threads\. Therefore, the primary key is composed of both the `Id` \(partition key\) and `ReplyDateTime` \(sort key\)\. The following Java code example scans the entire table\. The `ScanRequest` instance specifies the name of the table to scan\.

**Example**  

```
AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard().build();

ScanRequest scanRequest = new ScanRequest()
    .withTableName("Reply");

ScanResult result = client.scan(scanRequest);
for (Map<String, AttributeValue> item : result.getItems()){
    printItem(item);
}
```

## Specifying optional parameters<a name="DocumentAPIJavaScanningOptions"></a>

The `scan` method supports several optional parameters\. For example, you can optionally use a filter expression to filter the scan result\. In a filter expression, you can specify a condition and attribute names and values on which you want the condition evaluated\. For more information, see [Scan](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Scan.html)\.

The following Java example scans the `ProductCatalog` table to find items that are priced less than 0\. The example specifies the following optional parameters:
+ A filter expression to retrieve only the items priced less than 0 \(error condition\)\. 
+ A list of attributes to retrieve for items in the query results\.

**Example**  

```
Map<String, AttributeValue> expressionAttributeValues =
    new HashMap<String, AttributeValue>();
expressionAttributeValues.put(":val", new AttributeValue().withN("0"));

ScanRequest scanRequest = new ScanRequest()
    .withTableName("ProductCatalog")
    .withFilterExpression("Price < :val")
    .withProjectionExpression("Id")
    .withExpressionAttributeValues(expressionAttributeValues);


ScanResult result = client.scan(scanRequest);
for (Map<String, AttributeValue> item : result.getItems()) {
    printItem(item);
}
```

You can also optionally limit the page size, or the number of items per page, by using the `withLimit` method of the scan request\. Each time you run the `scan` method, you get one page of results that has the specified number of items\. To fetch the next page, you run the `scan` method again by providing the primary key value of the last item in the previous page so that the `scan` method can return the next set of items\. You provide this information in the request by using the `withExclusiveStartKey` method\. Initially, the parameter of this method can be null\. To retrieve subsequent pages, you must update this property value to the primary key of the last item in the preceding page\.

The following Java code example scans the `ProductCatalog` table\. In the request, the `withLimit` and `withExclusiveStartKey` methods are used\. The `do/while` loop continues to scan one page at time until the `getLastEvaluatedKey` method of the result returns a value of null\. 

**Example**  

```
Map<String, AttributeValue> lastKeyEvaluated = null;
do {
    ScanRequest scanRequest = new ScanRequest()
        .withTableName("ProductCatalog")
        .withLimit(10)
        .withExclusiveStartKey(lastKeyEvaluated);

    ScanResult result = client.scan(scanRequest);
    for (Map<String, AttributeValue> item : result.getItems()){
        printItem(item);
    }
    lastKeyEvaluated = result.getLastEvaluatedKey();
} while (lastKeyEvaluated != null);
```

## Example \- scan using Java<a name="DocumentAPIJavaScanExample"></a>

The following Java code example provides a working sample that scans the `ProductCatalog` table to find items that are priced less than 100\. 

**Note**  
The SDK for Java also provides an object persistence model, enabling you to map your client\-side classes to DynamoDB tables\. This approach can reduce the amount of code that you have to write\. For more information, see [Java: DynamoDBMapper](DynamoDBMapper.md)\.

**Note**  
This code example assumes that you have already loaded data into DynamoDB for your account by following the instructions in the [Creating tables and loading data for code examples in DynamoDB](SampleData.md) section\.  
For step\-by\-step instructions to run the following example, see [Java code examples](CodeSamples.Java.md)\.

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

import java.util.HashMap;
import java.util.Iterator;
import java.util.Map;

import com.amazonaws.services.dynamodbv2.AmazonDynamoDB;
import com.amazonaws.services.dynamodbv2.AmazonDynamoDBClientBuilder;
import com.amazonaws.services.dynamodbv2.document.DynamoDB;
import com.amazonaws.services.dynamodbv2.document.Item;
import com.amazonaws.services.dynamodbv2.document.ItemCollection;
import com.amazonaws.services.dynamodbv2.document.ScanOutcome;
import com.amazonaws.services.dynamodbv2.document.Table;

public class DocumentAPIScan {

    static AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard().build();
    static DynamoDB dynamoDB = new DynamoDB(client);
    static String tableName = "ProductCatalog";

    public static void main(String[] args) throws Exception {

        findProductsForPriceLessThanOneHundred();
    }

    private static void findProductsForPriceLessThanOneHundred() {

        Table table = dynamoDB.getTable(tableName);

        Map<String, Object> expressionAttributeValues = new HashMap<String, Object>();
        expressionAttributeValues.put(":pr", 100);

        ItemCollection<ScanOutcome> items = table.scan("Price < :pr", // FilterExpression
            "Id, Title, ProductCategory, Price", // ProjectionExpression
            null, // ExpressionAttributeNames - not used in this example
            expressionAttributeValues);

        System.out.println("Scan of " + tableName + " for items with a price less than 100.");
        Iterator<Item> iterator = items.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next().toJSONPretty());
        }
    }

}
```

## Example \- parallel scan using Java<a name="DocumentAPIJavaParallelScanExample"></a>

The following Java code example demonstrates a parallel scan\. The program deletes and re\-creates a table named `ParallelScanTest`, and then loads the table with data\. When the data load is finished, the program spawns multiple threads and issues parallel `Scan` requests\. The program prints runtime statistics for each parallel request\.

**Note**  
The SDK for Java also provides an object persistence model, enabling you to map your client\-side classes to DynamoDB tables\. This approach can reduce the amount of code that you have to write\. For more information, see [Java: DynamoDBMapper](DynamoDBMapper.md)\.

**Note**  
This code example assumes that you have already loaded data into DynamoDB for your account by following the instructions in the [Creating tables and loading data for code examples in DynamoDB](SampleData.md) section\.  
For step\-by\-step instructions to run the following example, see [Java code examples](CodeSamples.Java.md)\.

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

import java.util.ArrayList;
import java.util.Arrays;
import java.util.HashSet;
import java.util.Iterator;
import java.util.List;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;

import com.amazonaws.AmazonServiceException;
import com.amazonaws.services.dynamodbv2.AmazonDynamoDB;
import com.amazonaws.services.dynamodbv2.AmazonDynamoDBClientBuilder;
import com.amazonaws.services.dynamodbv2.document.DynamoDB;
import com.amazonaws.services.dynamodbv2.document.Item;
import com.amazonaws.services.dynamodbv2.document.ItemCollection;
import com.amazonaws.services.dynamodbv2.document.ScanOutcome;
import com.amazonaws.services.dynamodbv2.document.Table;
import com.amazonaws.services.dynamodbv2.document.spec.ScanSpec;
import com.amazonaws.services.dynamodbv2.model.AttributeDefinition;
import com.amazonaws.services.dynamodbv2.model.KeySchemaElement;
import com.amazonaws.services.dynamodbv2.model.KeyType;
import com.amazonaws.services.dynamodbv2.model.ProvisionedThroughput;

public class DocumentAPIParallelScan {

    // total number of sample items
    static int scanItemCount = 300;

    // number of items each scan request should return
    static int scanItemLimit = 10;

    // number of logical segments for parallel scan
    static int parallelScanThreads = 16;

    // table that will be used for scanning
    static String parallelScanTestTableName = "ParallelScanTest";

    static AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard().build();
    static DynamoDB dynamoDB = new DynamoDB(client);

    public static void main(String[] args) throws Exception {
        try {

            // Clean up the table
            deleteTable(parallelScanTestTableName);
            createTable(parallelScanTestTableName, 10L, 5L, "Id", "N");

            // Upload sample data for scan
            uploadSampleProducts(parallelScanTestTableName, scanItemCount);

            // Scan the table using multiple threads
            parallelScan(parallelScanTestTableName, scanItemLimit, parallelScanThreads);
        }
        catch (AmazonServiceException ase) {
            System.err.println(ase.getMessage());
        }
    }

    private static void parallelScan(String tableName, int itemLimit, int numberOfThreads) {
        System.out.println(
            "Scanning " + tableName + " using " + numberOfThreads + " threads " + itemLimit + " items at a time");
        ExecutorService executor = Executors.newFixedThreadPool(numberOfThreads);

        // Divide DynamoDB table into logical segments
        // Create one task for scanning each segment
        // Each thread will be scanning one segment
        int totalSegments = numberOfThreads;
        for (int segment = 0; segment < totalSegments; segment++) {
            // Runnable task that will only scan one segment
            ScanSegmentTask task = new ScanSegmentTask(tableName, itemLimit, totalSegments, segment);

            // Execute the task
            executor.execute(task);
        }

        shutDownExecutorService(executor);
    }

    // Runnable task for scanning a single segment of a DynamoDB table
    private static class ScanSegmentTask implements Runnable {

        // DynamoDB table to scan
        private String tableName;

        // number of items each scan request should return
        private int itemLimit;

        // Total number of segments
        // Equals to total number of threads scanning the table in parallel
        private int totalSegments;

        // Segment that will be scanned with by this task
        private int segment;

        public ScanSegmentTask(String tableName, int itemLimit, int totalSegments, int segment) {
            this.tableName = tableName;
            this.itemLimit = itemLimit;
            this.totalSegments = totalSegments;
            this.segment = segment;
        }

        @Override
        public void run() {
            System.out.println("Scanning " + tableName + " segment " + segment + " out of " + totalSegments
                + " segments " + itemLimit + " items at a time...");
            int totalScannedItemCount = 0;

            Table table = dynamoDB.getTable(tableName);

            try {
                ScanSpec spec = new ScanSpec().withMaxResultSize(itemLimit).withTotalSegments(totalSegments)
                    .withSegment(segment);

                ItemCollection<ScanOutcome> items = table.scan(spec);
                Iterator<Item> iterator = items.iterator();

                Item currentItem = null;
                while (iterator.hasNext()) {
                    totalScannedItemCount++;
                    currentItem = iterator.next();
                    System.out.println(currentItem.toString());
                }

            }
            catch (Exception e) {
                System.err.println(e.getMessage());
            }
            finally {
                System.out.println("Scanned " + totalScannedItemCount + " items from segment " + segment + " out of "
                    + totalSegments + " of " + tableName);
            }
        }
    }

    private static void uploadSampleProducts(String tableName, int itemCount) {
        System.out.println("Adding " + itemCount + " sample items to " + tableName);
        for (int productIndex = 0; productIndex < itemCount; productIndex++) {
            uploadProduct(tableName, productIndex);
        }
    }

    private static void uploadProduct(String tableName, int productIndex) {

        Table table = dynamoDB.getTable(tableName);

        try {
            System.out.println("Processing record #" + productIndex);

            Item item = new Item().withPrimaryKey("Id", productIndex)
                .withString("Title", "Book " + productIndex + " Title").withString("ISBN", "111-1111111111")
                .withStringSet("Authors", new HashSet<String>(Arrays.asList("Author1"))).withNumber("Price", 2)
                .withString("Dimensions", "8.5 x 11.0 x 0.5").withNumber("PageCount", 500)
                .withBoolean("InPublication", true).withString("ProductCategory", "Book");
            table.putItem(item);

        }
        catch (Exception e) {
            System.err.println("Failed to create item " + productIndex + " in " + tableName);
            System.err.println(e.getMessage());
        }
    }

    private static void deleteTable(String tableName) {
        try {

            Table table = dynamoDB.getTable(tableName);
            table.delete();
            System.out.println("Waiting for " + tableName + " to be deleted...this may take a while...");
            table.waitForDelete();

        }
        catch (Exception e) {
            System.err.println("Failed to delete table " + tableName);
            e.printStackTrace(System.err);
        }
    }

    private static void createTable(String tableName, long readCapacityUnits, long writeCapacityUnits,
        String partitionKeyName, String partitionKeyType) {

        createTable(tableName, readCapacityUnits, writeCapacityUnits, partitionKeyName, partitionKeyType, null, null);
    }

    private static void createTable(String tableName, long readCapacityUnits, long writeCapacityUnits,
        String partitionKeyName, String partitionKeyType, String sortKeyName, String sortKeyType) {

        try {
            System.out.println("Creating table " + tableName);

            List<KeySchemaElement> keySchema = new ArrayList<KeySchemaElement>();
            keySchema.add(new KeySchemaElement().withAttributeName(partitionKeyName).withKeyType(KeyType.HASH)); // Partition
                                                                                                                 // key

            List<AttributeDefinition> attributeDefinitions = new ArrayList<AttributeDefinition>();
            attributeDefinitions
                .add(new AttributeDefinition().withAttributeName(partitionKeyName).withAttributeType(partitionKeyType));

            if (sortKeyName != null) {
                keySchema.add(new KeySchemaElement().withAttributeName(sortKeyName).withKeyType(KeyType.RANGE)); // Sort
                                                                                                                 // key
                attributeDefinitions
                    .add(new AttributeDefinition().withAttributeName(sortKeyName).withAttributeType(sortKeyType));
            }

            Table table = dynamoDB.createTable(tableName, keySchema, attributeDefinitions, new ProvisionedThroughput()
                .withReadCapacityUnits(readCapacityUnits).withWriteCapacityUnits(writeCapacityUnits));
            System.out.println("Waiting for " + tableName + " to be created...this may take a while...");
            table.waitForActive();

        }
        catch (Exception e) {
            System.err.println("Failed to create table " + tableName);
            e.printStackTrace(System.err);
        }
    }

    private static void shutDownExecutorService(ExecutorService executor) {
        executor.shutdown();
        try {
            if (!executor.awaitTermination(10, TimeUnit.SECONDS)) {
                executor.shutdownNow();
            }
        }
        catch (InterruptedException e) {
            executor.shutdownNow();

            // Preserve interrupt status
            Thread.currentThread().interrupt();
        }
    }
}
```