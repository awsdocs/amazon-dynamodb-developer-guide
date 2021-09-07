# Modifying an Existing SDK for Java 1\.x Application to Use DAX<a name="DAX.client.modify-your-app.java-sdk-v1"></a>

If you already have a Java application that uses Amazon DynamoDB, you have to modify it so that it can access your DynamoDB Accelerator \(DAX\) cluster\. You don't have to rewrite the entire application because the DAX Java client is similar to the DynamoDB low\-level client included in the AWS SDK for Java\.

**Note**  
These instructions are for applications using AWS SDK for Java 1\.x\. For applications using AWS SDK for Java 2\.x, see [Modifying an existing application to use DAX](DAX.client.modify-your-app.md)\.

Suppose that you have a DynamoDB table named `Music`\. The partition key for the table is `Artist`, and its sort key is `SongTitle`\. The following program reads an item directly from the `Music` table\.

```
import java.util.HashMap;

import com.amazonaws.services.dynamodbv2.AmazonDynamoDB;
import com.amazonaws.services.dynamodbv2.AmazonDynamoDBClientBuilder;
import com.amazonaws.services.dynamodbv2.model.AttributeValue;
import com.amazonaws.services.dynamodbv2.model.GetItemRequest;
import com.amazonaws.services.dynamodbv2.model.GetItemResult;

public class GetMusicItem {

    public static void main(String[] args) throws Exception {

        // Create a DynamoDB client
        AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard().build();

        HashMap<String, AttributeValue> key = new HashMap<String, AttributeValue>();
        key.put("Artist", new AttributeValue().withS("No One You Know"));
        key.put("SongTitle", new AttributeValue().withS("Scared of My Shadow"));

        GetItemRequest request = new GetItemRequest()
            .withTableName("Music").withKey(key);

        try {
            System.out.println("Attempting to read the item...");
            GetItemResult result = client.getItem(request);
            System.out.println("GetItem succeeded: " + result);

        } catch (Exception e) {
            System.err.println("Unable to read item");
            System.err.println(e.getMessage());
        }
    }
}
```

To modify the program, replace the DynamoDB client with a DAX client\.

```
import java.util.HashMap;

import com.amazonaws.services.dynamodbv2.AmazonDynamoDB;
import com.amazon.dax.client.dynamodbv2.AmazonDaxClientBuilder;
import com.amazonaws.services.dynamodbv2.model.AttributeValue;
import com.amazonaws.services.dynamodbv2.model.GetItemRequest;
import com.amazonaws.services.dynamodbv2.model.GetItemResult;

public class GetMusicItem {

    public static void main(String[] args) throws Exception {

    //Create a DAX client

    AmazonDaxClientBuilder daxClientBuilder = AmazonDaxClientBuilder.standard();
    daxClientBuilder.withRegion("us-east-1").withEndpointConfiguration("mydaxcluster.2cmrwl.clustercfg.dax.use1.cache.amazonaws.com:8111");
    AmazonDynamoDB client = daxClientBuilder.build();


       /*
       ** ...
       ** Remaining code omitted (it is identical)
       ** ...
       */

    }
}
```

## Using the DynamoDB Document API<a name="DAX.client.modify-your-app.document-api"></a>

The AWS SDK for Java provides a document interface for DynamoDB\. The document API acts as a wrapper around the low\-level DynamoDB client\. For more information, see [Document Interfaces](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Programming.SDKs.Interfaces.Document.html)\.

The document interface can also be used with the low\-level DAX client, as shown in the following example\.

```
import com.amazonaws.services.dynamodbv2.AmazonDynamoDB;
import com.amazon.dax.client.dynamodbv2.AmazonDaxClientBuilder;
import com.amazonaws.services.dynamodbv2.document.DynamoDB;
import com.amazonaws.services.dynamodbv2.document.GetItemOutcome;
import com.amazonaws.services.dynamodbv2.document.Table;

public class GetMusicItemWithDocumentApi {

    public static void main(String[] args) throws Exception {

        //Create a DAX client

        AmazonDaxClientBuilder daxClientBuilder = AmazonDaxClientBuilder.standard();
        daxClientBuilder.withRegion("us-east-1").withEndpointConfiguration("mydaxcluster.2cmrwl.clustercfg.dax.use1.cache.amazonaws.com:8111");
        AmazonDynamoDB client = daxClientBuilder.build();

        // Document client wrapper
        DynamoDB docClient = new DynamoDB(client);

        Table table = docClient.getTable("Music");

        try {
            System.out.println("Attempting to read the item...");
            GetItemOutcome outcome = table.tgetItemOutcome(
                "Artist", "No One You Know",
                "SongTitle", "Scared of My Shadow");
            System.out.println(outcome.getItem());
            System.out.println("GetItem succeeded: " + outcome);
        } catch (Exception e) {
            System.err.println("Unable to read item");
            System.err.println(e.getMessage());
        }

    }
}
```

## DAX Async Client<a name="DAX.client.async"></a>

The `AmazonDaxClient` is synchronous\. For a long\-running DAX API operation, such as a `Scan` of a large table, this can block program execution until the operation is complete\. If your program needs to perform other work while a DAX API operation is in progress, you can use `ClusterDaxAsyncClient` instead\.

The following program shows how to use `ClusterDaxAsyncClient`, along with Java `Future`, to implement a non\-blocking solution\.

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
import java.util.HashMap;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;

import com.amazon.dax.client.dynamodbv2.ClientConfig;
import com.amazon.dax.client.dynamodbv2.ClusterDaxAsyncClient;
import com.amazonaws.auth.profile.ProfileCredentialsProvider;
import com.amazonaws.handlers.AsyncHandler;
import com.amazonaws.services.dynamodbv2.AmazonDynamoDBAsync;
import com.amazonaws.services.dynamodbv2.model.AttributeValue;
import com.amazonaws.services.dynamodbv2.model.GetItemRequest;
import com.amazonaws.services.dynamodbv2.model.GetItemResult;

public class DaxAsyncClientDemo {
    public static void main(String[] args) throws Exception {

	ClientConfig daxConfig = new ClientConfig().withCredentialsProvider(new ProfileCredentialsProvider())
		.withEndpoints("mydaxcluster.2cmrwl.clustercfg.dax.use1.cache.amazonaws.com:8111");

	AmazonDynamoDBAsync client = new ClusterDaxAsyncClient(daxConfig);

        HashMap<String, AttributeValue> key = new HashMap<String, AttributeValue>();
        key.put("Artist", new AttributeValue().withS("No One You Know"));
        key.put("SongTitle", new AttributeValue().withS("Scared of My Shadow"));

	GetItemRequest request = new GetItemRequest()
	    .withTableName("Music").withKey(key);

	// Java Futures
	Future<GetItemResult> call = client.getItemAsync(request);
	while (!call.isDone()) {
	    // Do other processing while you're waiting for the response
	    System.out.println("Doing something else for a few seconds...");
	    Thread.sleep(3000);
	}
	// The results should be ready by now
	
	try {
	    call.get();
	    
	} catch (ExecutionException ee) {
	    // Futures always wrap errors as an ExecutionException.
	    // The *real* exception is stored as the cause of the
	    // ExecutionException
	    Throwable exception = ee.getCause();
	    System.out.println("Error getting item: " + exception.getMessage());
	}

	// Async callbacks
	call = client.getItemAsync(request, new AsyncHandler<GetItemRequest, GetItemResult>() {

	    @Override
	    public void onSuccess(GetItemRequest request, GetItemResult getItemResult) {
		System.out.println("Result: " + getItemResult);
	    }
	    
	    @Override
	    public void onError(Exception e) {
		System.out.println("Unable to read item");
		System.err.println(e.getMessage());
		// Callers can also test if exception is an instance of
		// AmazonServiceException or AmazonClientException and cast
		// it to get additional information
	    }

	});
	call.get();

    }
}
```