# TryDax\.java<a name="DAX.client.run-application-java.TryDax"></a>

The `TryDax.java` file contains the `main` method\. If you run the program with no command line parameters, it creates an Amazon DynamoDB client and uses that client for all API operations\. If you specify a DynamoDB Accelerator \(DAX\) cluster endpoint on the command line, the program also creates a DAX client and uses it for `GetItem`, `Query`, and `Scan` operations\.

You can modify the program in several ways:
+ Use the DAX client instead of the DynamoDB client\. For more information, see [Java and DAX](DAX.client.run-application-java.md)\.
+ Choose a different name for the test table\.
+ Modify the number of items written by changing the `helper.writeData` parameters\. The second parameter is the number of partition keys, and the third parameter is the number of sort keys\. By default, the program uses 1–10 for partition key values and 1–10 for sort key values, for a total of 100 items written to the table\. For more information, see [TryDaxHelper\.java](DAX.client.run-application-java.TryDaxHelper.md)\.
+ Modify the number of `GetItem`, `Query`, and `Scan` tests, and modify their parameters\.
+ Comment out the lines containing `helper.createTable` and `helper.deleteTable` \(if you don't want to create and delete the table each time you run the program\)\.

**Note**  
To run this program, you can set up Maven to use the client for the DAX SDK for Java and the AWS SDK for Java as dependencies\. For more information, see [Using the client as an Apache Maven dependency](DAX.client.java-sdk-v1.md#DAXClient.Maven)\.   
Alternatively, you can download and include both the DAX Java client and the AWS SDK for Java in your classpath\. See [Java and DAX](DAX.client.run-application-java.md) for an example of setting your `CLASSPATH` variable\.

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
import com.amazonaws.services.dynamodbv2.document.DynamoDB;

public class TryDax {

    public static void main(String[] args) throws Exception {
	
        TryDaxHelper helper = new TryDaxHelper();
        TryDaxTests tests = new TryDaxTests();

        DynamoDB ddbClient = helper.getDynamoDBClient();
        DynamoDB daxClient = null;
        if (args.length >= 1) {
            daxClient = helper.getDaxClient(args[0]);
        }

        String tableName = "TryDaxTable";

        System.out.println("Creating table...");
        helper.createTable(tableName, ddbClient);
        System.out.println("Populating table...");
        helper.writeData(tableName, ddbClient, 10, 10);

        DynamoDB testClient = null;
        if (daxClient != null) {
            testClient = daxClient;
        } else {
            testClient = ddbClient;
        }

        System.out.println("Running GetItem, Scan, and Query tests...");
        System.out.println("First iteration of each test will result in cache misses");
        System.out.println("Next iterations are cache hits\n");

        // GetItem
        tests.getItemTest(tableName, testClient, 1, 10, 5);

        // Query
        tests.queryTest(tableName, testClient, 5, 2, 9, 5);

        // Scan
        tests.scanTest(tableName, testClient, 5);

        helper.deleteTable(tableName, ddbClient);
    }

}
```