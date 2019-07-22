# Low\-Level Interfaces<a name="Programming.SDKs.Interfaces.LowLevel"></a>

Every language\-specific AWS SDK provides a low\-level interface for Amazon DynamoDB, with methods that closely resemble low\-level DynamoDB API requests\.

In some cases, you will need to identify the data types of the attributes using [Data Type Descriptors](Programming.LowLevelAPI.md#Programming.LowLevelAPI.DataTypeDescriptors), such as `S` for string or `N` for number\.

**Note**  
A low\-level interface is available in every language\-specific AWS SDK\.

The following Java program uses the low\-level interface of the AWS SDK for Java\. The program issues a `GetItem` request for a song in the `Music` table and prints the year that the song was released\.

The `com.amazonaws.services.dynamodbv2.AmazonDynamoDB` class implements the DynamoDB low\-level interface\.

```
package com.amazonaws.codesamples;

import java.util.HashMap;

import com.amazonaws.services.dynamodbv2.AmazonDynamoDB;
import com.amazonaws.services.dynamodbv2.AmazonDynamoDBClientBuilder;
import com.amazonaws.services.dynamodbv2.model.AttributeValue;
import com.amazonaws.services.dynamodbv2.model.GetItemRequest;
import com.amazonaws.services.dynamodbv2.model.GetItemResult;

public class MusicLowLevelDemo {

    public static void main(String[] args) {

        AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard().build();

        HashMap<String, AttributeValue> key = new HashMap<String, AttributeValue>();
        key.put("Artist", new AttributeValue().withS("No One You Know"));
        key.put("SongTitle", new AttributeValue().withS("Call Me Today"));

        GetItemRequest request = new GetItemRequest()
            .withTableName("Music")
            .withKey(key);

        try {
            GetItemResult result = client.getItem(request);
            if (result && result.getItem() != null) {
                AttributeValue year = result.getItem().get("Year");
                System.out.println("The song was released in " + year.getN());
            } else {
                System.out.println("No matching song was found");
            }
        } catch (Exception e) {
            System.err.println("Unable to retrieve data: ");
            System.err.println(e.getMessage());
        }
    }
}
```