# Querying Tables and Indexes: Java<a name="QueryingJavaDocumentAPI"></a>

The `Query` operation enables you to query a table or a secondary index in Amazon DynamoDB\. You must provide a partition key value and an equality condition\. If the table or index has a sort key, you can refine the results by providing a sort key value and a condition\.

**Note**  
The AWS SDK for Java also provides an object persistence model, enabling you to map your client\-side classes to DynamoDB tables\. This approach can reduce the amount of code you have to write\. For more information, see [Java: DynamoDBMapper](DynamoDBMapper.md)\.

The following are the steps to retrieve an item using the AWS SDK for Java Document API\. 

1. Create an instance of the `DynamoDB` class\.

1. Create an instance of the `Table` class to represent the table you want to work with\.

1. Call the `query` method of the `Table` instance\. You must specify the partition key value of the items that you want to retrieve, along with any optional query parameters\.

The response includes an `ItemCollection` object that provides all items returned by the query\.

The following Java code example demonstrates the preceding tasks\. The example assumes that you have a `Reply` table that stores replies for forum threads\. For more information, see [Creating Tables and Loading Data for Code Examples in DynamoDB](SampleData.md)\.

 ` Reply ( Id, ReplyDateTime, ... ) ` 

Each forum thread has a unique ID and can have zero or more replies\. Therefore, the `Id` attribute of the `Reply` table is composed of both the forum name and forum subject\. `Id` \(partition key\) and `ReplyDateTime` \(sort key\) make up the composite primary key for the table\.

The following query retrieves all replies for a specific thread subject\. The query requires both the table name and the `Subject` value\.

**Example**  

```
AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard()
.withRegion(Regions.US_WEST_2).build();
DynamoDB dynamoDB = new DynamoDB(client);

Table table = dynamoDB.getTable("Reply");

QuerySpec spec = new QuerySpec()
    .withKeyConditionExpression("Id = :v_id")
    .withValueMap(new ValueMap()
        .withString(":v_id", "Amazon DynamoDB#DynamoDB Thread 1"));

ItemCollection<QueryOutcome> items = table.query(spec);

Iterator<Item> iterator = items.iterator();
Item item = null;
while (iterator.hasNext()) {
    item = iterator.next();
    System.out.println(item.toJSONPretty());
}
```

## Specifying Optional Parameters<a name="DocumentAPIJavaQueryingOptions"></a>

The `query` method supports several optional parameters\. For example, you can optionally narrow the results from the preceding query to return replies in the past two weeks by specifying a condition\. The condition is called a sort key condition, because DynamoDB evaluates the query condition that you specify against the sort key of the primary key\. You can specify other optional parameters to retrieve only a specific list of attributes from items in the query result\.

The following Java code example retrieves forum thread replies posted in the past 15 days\. The example specifies optional parameters using the following:
+ A `KeyConditionExpression` to retrieve the replies from a specific discussion forum \(partition key\) and, within that set of items, replies that were posted within the last 15 days \(sort key\)\.
+ A `FilterExpression` to return only the replies from a specific user\. The filter is applied after the query is processed, but before the results are returned to the user\.
+ A `ValueMap` to define the actual values for the `KeyConditionExpression` placeholders\.
+ A `ConsistentRead` setting of true, to request a strongly consistent read\.

This example uses a `QuerySpec` object that gives access to all of the low\-level `Query` input parameters\.

**Example**  

```
Table table = dynamoDB.getTable("Reply");

long twoWeeksAgoMilli = (new Date()).getTime() - (15L*24L*60L*60L*1000L);
Date twoWeeksAgo = new Date();
twoWeeksAgo.setTime(twoWeeksAgoMilli);
SimpleDateFormat df = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'");
String twoWeeksAgoStr = df.format(twoWeeksAgo);

QuerySpec spec = new QuerySpec()
    .withKeyConditionExpression("Id = :v_id and ReplyDateTime > :v_reply_dt_tm")
    .withFilterExpression("PostedBy = :v_posted_by")
    .withValueMap(new ValueMap()
        .withString(":v_id", "Amazon DynamoDB#DynamoDB Thread 1")
        .withString(":v_reply_dt_tm", twoWeeksAgoStr)
        .withString(":v_posted_by", "User B"))
    .withConsistentRead(true);

ItemCollection<QueryOutcome> items = table.query(spec);

Iterator<Item> iterator = items.iterator();
while (iterator.hasNext()) {
    System.out.println(iterator.next().toJSONPretty());
}
```

You can also optionally limit the number of items per page by using the `withMaxPageSize` method\. When you call the `query` method, you get an `ItemCollection` that contains the resulting items\. You can then step through the results, processing one page at a time, until there are no more pages\.

The following Java code example modifies the query specification shown previously\. This time, the query spec uses the `withMaxPageSize` method\. The `Page` class provides an iterator that allows the code to process the items on each page\.

**Example**  

```
spec.withMaxPageSize(10);

ItemCollection<QueryOutcome> items = table.query(spec);

// Process each page of results
int pageNum = 0;
for (Page<Item, QueryOutcome> page : items.pages()) {

    System.out.println("\nPage: " + ++pageNum);

    // Process each item on the current page
    Iterator<Item> item = page.iterator();
    while (item.hasNext()) {
        System.out.println(item.next().toJSONPretty());
    }
}
```

## Example \- Query Using Java<a name="DocumentAPIJavaQueryExample"></a>

The following tables store information about a collection of forums\. For more information, see [Creating Tables and Loading Data for Code Examples in DynamoDB](SampleData.md)\.

**Note**  
The SDK for Java also provides an object persistence model, enabling you to map your client\-side classes to DynamoDB tables\. This approach can reduce the amount of code you have to write\. For more information, see [Java: DynamoDBMapper](DynamoDBMapper.md)\.



**Example**  

```
Forum ( Name, ... )
Thread ( ForumName, Subject, Message, LastPostedBy, LastPostDateTime, ...)
Reply ( Id, ReplyDateTime, Message, PostedBy, ...)
```

In this Java code example, you run variations of finding replies for a thread "DynamoDB Thread 1" in forum "DynamoDB"\. 
+ Find replies for a thread\.
+ Find replies for a thread, specifying a limit on the number of items per page of results\. If the number of items in the result set exceeds the page size, you get only the first page of results\. This coding pattern ensures that your code processes all the pages in the query result\.
+ Find replies in the last 15 days\.
+ Find replies in a specific date range\. 

  The preceding two queries show how you can specify sort key conditions to narrow the query results and use other optional query parameters\. 

**Note**  
This code example assumes that you have already loaded data into DynamoDB for your account by following the instructions in the [Creating Tables and Loading Data for Code Examples in DynamoDB](SampleData.md) section\.  
For step\-by\-step instructions to run the following example, see [Java Code Examples](CodeSamples.Java.md)\.

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

import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.Iterator;

import com.amazonaws.services.dynamodbv2.AmazonDynamoDB;
import com.amazonaws.services.dynamodbv2.AmazonDynamoDBClientBuilder;
import com.amazonaws.services.dynamodbv2.document.DynamoDB;
import com.amazonaws.services.dynamodbv2.document.Item;
import com.amazonaws.services.dynamodbv2.document.ItemCollection;
import com.amazonaws.services.dynamodbv2.document.Page;
import com.amazonaws.services.dynamodbv2.document.QueryOutcome;
import com.amazonaws.services.dynamodbv2.document.Table;
import com.amazonaws.services.dynamodbv2.document.spec.QuerySpec;
import com.amazonaws.services.dynamodbv2.document.utils.ValueMap;

public class DocumentAPIQuery {

    static AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard().build();
    static DynamoDB dynamoDB = new DynamoDB(client);

    static String tableName = "Reply";

    public static void main(String[] args) throws Exception {

        String forumName = "Amazon DynamoDB";
        String threadSubject = "DynamoDB Thread 1";

        findRepliesForAThread(forumName, threadSubject);
        findRepliesForAThreadSpecifyOptionalLimit(forumName, threadSubject);
        findRepliesInLast15DaysWithConfig(forumName, threadSubject);
        findRepliesPostedWithinTimePeriod(forumName, threadSubject);
        findRepliesUsingAFilterExpression(forumName, threadSubject);
    }

    private static void findRepliesForAThread(String forumName, String threadSubject) {

        Table table = dynamoDB.getTable(tableName);

        String replyId = forumName + "#" + threadSubject;

        QuerySpec spec = new QuerySpec().withKeyConditionExpression("Id = :v_id")
            .withValueMap(new ValueMap().withString(":v_id", replyId));

        ItemCollection<QueryOutcome> items = table.query(spec);

        System.out.println("\nfindRepliesForAThread results:");

        Iterator<Item> iterator = items.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next().toJSONPretty());
        }

    }

    private static void findRepliesForAThreadSpecifyOptionalLimit(String forumName, String threadSubject) {

        Table table = dynamoDB.getTable(tableName);

        String replyId = forumName + "#" + threadSubject;

        QuerySpec spec = new QuerySpec().withKeyConditionExpression("Id = :v_id")
            .withValueMap(new ValueMap().withString(":v_id", replyId)).withMaxPageSize(1);

        ItemCollection<QueryOutcome> items = table.query(spec);

        System.out.println("\nfindRepliesForAThreadSpecifyOptionalLimit results:");

        // Process each page of results
        int pageNum = 0;
        for (Page<Item, QueryOutcome> page : items.pages()) {

            System.out.println("\nPage: " + ++pageNum);

            // Process each item on the current page
            Iterator<Item> item = page.iterator();
            while (item.hasNext()) {
                System.out.println(item.next().toJSONPretty());
            }
        }
    }

    private static void findRepliesInLast15DaysWithConfig(String forumName, String threadSubject) {

        Table table = dynamoDB.getTable(tableName);

        long twoWeeksAgoMilli = (new Date()).getTime() - (15L * 24L * 60L * 60L * 1000L);
        Date twoWeeksAgo = new Date();
        twoWeeksAgo.setTime(twoWeeksAgoMilli);
        SimpleDateFormat df = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'");
        String twoWeeksAgoStr = df.format(twoWeeksAgo);

        String replyId = forumName + "#" + threadSubject;

        QuerySpec spec = new QuerySpec().withProjectionExpression("Message, ReplyDateTime, PostedBy")
            .withKeyConditionExpression("Id = :v_id and ReplyDateTime <= :v_reply_dt_tm")
            .withValueMap(new ValueMap().withString(":v_id", replyId).withString(":v_reply_dt_tm", twoWeeksAgoStr));

        ItemCollection<QueryOutcome> items = table.query(spec);

        System.out.println("\nfindRepliesInLast15DaysWithConfig results:");
        Iterator<Item> iterator = items.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next().toJSONPretty());
        }

    }

    private static void findRepliesPostedWithinTimePeriod(String forumName, String threadSubject) {

        Table table = dynamoDB.getTable(tableName);

        long startDateMilli = (new Date()).getTime() - (15L * 24L * 60L * 60L * 1000L);
        long endDateMilli = (new Date()).getTime() - (5L * 24L * 60L * 60L * 1000L);
        java.text.SimpleDateFormat df = new java.text.SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'");
        String startDate = df.format(startDateMilli);
        String endDate = df.format(endDateMilli);

        String replyId = forumName + "#" + threadSubject;

        QuerySpec spec = new QuerySpec().withProjectionExpression("Message, ReplyDateTime, PostedBy")
            .withKeyConditionExpression("Id = :v_id and ReplyDateTime between :v_start_dt and :v_end_dt")
            .withValueMap(new ValueMap().withString(":v_id", replyId).withString(":v_start_dt", startDate)
                .withString(":v_end_dt", endDate));

        ItemCollection<QueryOutcome> items = table.query(spec);

        System.out.println("\nfindRepliesPostedWithinTimePeriod results:");
        Iterator<Item> iterator = items.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next().toJSONPretty());
        }
    }

    private static void findRepliesUsingAFilterExpression(String forumName, String threadSubject) {

        Table table = dynamoDB.getTable(tableName);

        String replyId = forumName + "#" + threadSubject;

        QuerySpec spec = new QuerySpec().withProjectionExpression("Message, ReplyDateTime, PostedBy")
            .withKeyConditionExpression("Id = :v_id").withFilterExpression("PostedBy = :v_postedby")
            .withValueMap(new ValueMap().withString(":v_id", replyId).withString(":v_postedby", "User B"));

        ItemCollection<QueryOutcome> items = table.query(spec);

        System.out.println("\nfindRepliesUsingAFilterExpression results:");
        Iterator<Item> iterator = items.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next().toJSONPretty());
        }
    }

}
```