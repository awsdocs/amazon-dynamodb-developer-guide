# Document interfaces<a name="Programming.SDKs.Interfaces.Document"></a>

Many AWS SDKs provide a document interface, allowing you to perform data plane operations \(create, read, update, delete\) on tables and indexes\. With a document interface, you do not need to specify [Data type descriptors](Programming.LowLevelAPI.md#Programming.LowLevelAPI.DataTypeDescriptors)\. The data types are implied by the semantics of the data itself\. These AWS SDKs also provide methods to easily convert JSON documents to and from native Amazon DynamoDB data types\.

**Note**  
Document interfaces are available in the AWS SDKs for [ Java](https://aws.amazon.com/sdk-for-java), [\.NET](https://aws.amazon.com/sdk-for-net), [Node\.js](https://aws.amazon.com/sdk-for-node-js), and [JavaScript in the browser](https://aws.amazon.com/sdk-for-browser)\.

The following Java program uses the document interface of the AWS SDK for Java\. The program creates a `Table` object that represents the `Music` table, and then asks that object to use `GetItem` to retrieve a song\. The program then prints the year that the song was released\.

The `com.amazonaws.services.dynamodbv2.document.DynamoDB` class implements the DynamoDB document interface\. Note how `DynamoDB` acts as a wrapper around the low\-level client \(`AmazonDynamoDB`\)\.

```
package com.amazonaws.codesamples.gsg;

import com.amazonaws.services.dynamodbv2.AmazonDynamoDB;
import com.amazonaws.services.dynamodbv2.AmazonDynamoDBClientBuilder;
import com.amazonaws.services.dynamodbv2.document.DynamoDB;
import com.amazonaws.services.dynamodbv2.document.GetItemOutcome;
import com.amazonaws.services.dynamodbv2.document.Table;

public class MusicDocumentDemo {

    public static void main(String[] args) {

        AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard().build();
        DynamoDB docClient = new DynamoDB(client);

        Table table = docClient.getTable("Music");
        GetItemOutcome outcome = table.getItemOutcome(
                "Artist", "No One You Know",
                "SongTitle", "Call Me Today");

        int year = outcome.getItem().getInt("Year");
        System.out.println("The song was released in " + year);

    }
}
```