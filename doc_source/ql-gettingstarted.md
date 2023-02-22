# Getting started with PartiQL for DynamoDB<a name="ql-gettingstarted"></a>

This section describes how to use PartiQL for DynamoDB from the Amazon DynamoDB console, the AWS Command Line Interface \(AWS CLI\), and DynamoDB APIs\.

In the following examples, the DynamoDB table that is defined in the [Getting started with DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GettingStartedDynamoDB.html) tutorial is a pre\-requisite\.

For information about using the DynamoDB console, AWS Command Line Interface, or DynamoDB APIs to access DynamoDB, see [Accessing DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/AccessingDynamoDB.html)\.

To [download](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/workbench.settingup.html) and use the [NoSQL workbench](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/workbench.html) to build [PartiQL for DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ql-reference.html) statements choose **PartiQL operations** at the top right corner of the NoSQL Workbench for DynamoDB [Operation builder](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/workbench.querybuilder.operationbuilder.html)\.

------
#### [ Console ]

![\[\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/partiqlgettingstarted.png)

**Note**  
PartiQL for DynamoDB is only available in the new DynamoDB console\. To use the new DynamoDB console, choose **Try the Preview of the new console** in the navigation pane on the left side of the console\. 

1. Sign in to the AWS Management Console and open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. In the navigation pane on the left side of the console, choose **PartiQL editor**\.

1. Choose the **Music** table\.

1. Choose **Query table**\. This action generates a query that will not result in a full table scan\.

1. Replace `partitionKeyValue` with the string value `Acme Band`\. Replace `sortKeyValue` with the string value `Happy Day`\.

1. Choose the **Run** button\. 

1. You can view the results of the query by choosing the **Table view** or the **JSON view** buttons\. 

------
#### [ NoSQL workbench ]

![\[\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/partiql.single.png)

1. Choose **PartiQL statement**\.

1. Enter the following PartiQL [SELECT statement](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ql-reference.select.html) 

   ```
   SELECT *                                         
   FROM Music  
   WHERE Artist=? and SongTitle=?
   ```

1. To specify a value for the `Artist` and `SongTitle` parameters:

   1. Choose **Optional request parameters**\.

   1. Choose **Add new parameters**\.

   1. Choose the attribute type **string** and value `Acme Band`\.

   1. Repeat steps b and c, and choose type **string** and value `PartiQL Rocks`\. 

1. If you want to generate code, choose **Generate code**\.

   Select your desired language from the displayed tabs\. You can now copy this code and use it in your application\.

1. If you want the operation to be run immediately, choose **Run**\.

------
#### [ AWS CLI ]

1. Create an item in the `Music` table using the INSERT PartiQL statement\. 

   ```
   aws dynamodb execute-statement --statement "INSERT INTO Music  \
   					    VALUE  \
   					    {'Artist':'Acme Band','SongTitle':'PartiQL Rocks'}"
   ```

1. Retrieve an item from the Music table using the SELECT PartiQL statement\.

   ```
   aws dynamodb execute-statement --statement "SELECT * FROM Music   \
                                               WHERE Artist='Acme Band' AND SongTitle='PartiQL Rocks'"
   ```

1. Update an item in the `Music` table using the UPDATE PartiQL statement\.

   ```
   aws dynamodb execute-statement --statement "UPDATE Music  \
                                               SET AwardsWon=1  \
                                               SET AwardDetail={'Grammys':[2020, 2018]}  \
                                               WHERE Artist='Acme Band' AND SongTitle='PartiQL Rocks'"
   ```

   Add a list value for an item in the `Music` table\. 

   ```
   aws dynamodb execute-statement --statement "UPDATE Music  \
                                               SET AwardDetail.Grammys =list_append(AwardDetail.Grammys,[2016])  \
                                               WHERE Artist='Acme Band' AND SongTitle='PartiQL Rocks'"
   ```

   Remove a list value for an item in the `Music` table\. 

   ```
   aws dynamodb execute-statement --statement "UPDATE Music  \
                                               REMOVE AwardDetail.Grammys[2]  \
                                               WHERE Artist='Acme Band' AND SongTitle='PartiQL Rocks'"
   ```

   Add a new map member for an item in the `Music` table\. 

   ```
   aws dynamodb execute-statement --statement "UPDATE Music  \
                                               SET AwardDetail.BillBoard=[2020]  \
                                               WHERE Artist='Acme Band' AND SongTitle='PartiQL Rocks'"
   ```

   Add a new string set attribute for an item in the `Music` table\. 

   ```
   aws dynamodb execute-statement --statement "UPDATE Music  \
                                               SET BandMembers =<<'member1', 'member2'>>  \
                                               WHERE Artist='Acme Band' AND SongTitle='PartiQL Rocks'"
   ```

   Update a string set attribute for an item in the `Music` table\. 

   ```
   aws dynamodb execute-statement --statement "UPDATE Music  \
                                               SET BandMembers =set_add(BandMembers, <<'newmember'>>)  \
                                               WHERE Artist='Acme Band' AND SongTitle='PartiQL Rocks'"
   ```

1. Delete an item from the `Music` table using the DELETE PartiQL statement\.

   ```
   aws dynamodb execute-statement --statement "DELETE  FROM Music  \
       WHERE Artist='Acme Band' AND SongTitle='PartiQL Rocks'"
   ```

------
#### [ Java ]

```
import java.util.ArrayList;
import java.util.List;

import com.amazonaws.AmazonClientException;
import com.amazonaws.AmazonServiceException;
import com.amazonaws.services.dynamodbv2.AmazonDynamoDB;
import com.amazonaws.services.dynamodbv2.AmazonDynamoDBClientBuilder;
import com.amazonaws.services.dynamodbv2.model.AttributeValue;
import com.amazonaws.services.dynamodbv2.model.ConditionalCheckFailedException;
import com.amazonaws.services.dynamodbv2.model.ExecuteStatementRequest;
import com.amazonaws.services.dynamodbv2.model.ExecuteStatementResult;
import com.amazonaws.services.dynamodbv2.model.InternalServerErrorException;
import com.amazonaws.services.dynamodbv2.model.ItemCollectionSizeLimitExceededException;
import com.amazonaws.services.dynamodbv2.model.ProvisionedThroughputExceededException;
import com.amazonaws.services.dynamodbv2.model.RequestLimitExceededException;
import com.amazonaws.services.dynamodbv2.model.ResourceNotFoundException;
import com.amazonaws.services.dynamodbv2.model.TransactionConflictException;

public class DynamoDBPartiQGettingStarted {

    public static void main(String[] args) {
        // Create the DynamoDB Client with the region you want
        AmazonDynamoDB dynamoDB = createDynamoDbClient("us-west-1");

        try {
            // Create ExecuteStatementRequest
            ExecuteStatementRequest executeStatementRequest = new ExecuteStatementRequest();
            List<AttributeValue> parameters= getPartiQLParameters();

            //Create an item in the Music table using the INSERT PartiQL statement
            processResults(executeStatementRequest(dynamoDB, "INSERT INTO Music value {'Artist':?,'SongTitle':?}", parameters));

            //Retrieve an item from the Music table using the SELECT PartiQL statement.
            processResults(executeStatementRequest(dynamoDB, "SELECT * FROM Music  where Artist=? and SongTitle=?", parameters));

            //Update an item in the Music table using the UPDATE PartiQL statement.
            processResults(executeStatementRequest(dynamoDB, "UPDATE Music SET AwardsWon=1 SET AwardDetail={'Grammys':[2020, 2018]}  where Artist=? and SongTitle=?", parameters));

            //Add a list value for an item in the Music table.
            processResults(executeStatementRequest(dynamoDB, "UPDATE Music SET AwardDetail.Grammys =list_append(AwardDetail.Grammys,[2016])  where Artist=? and SongTitle=?", parameters));

            //Remove a list value for an item in the Music table.
            processResults(executeStatementRequest(dynamoDB, "UPDATE Music REMOVE AwardDetail.Grammys[2]   where Artist=? and SongTitle=?", parameters));

            //Add a new map member for an item in the Music table.
            processResults(executeStatementRequest(dynamoDB, "UPDATE Music set AwardDetail.BillBoard=[2020] where Artist=? and SongTitle=?", parameters));

            //Add a new string set attribute for an item in the Music table.
            processResults(executeStatementRequest(dynamoDB, "UPDATE Music SET BandMembers =<<'member1', 'member2'>> where Artist=? and SongTitle=?", parameters));

            //update a string set attribute for an item in the Music table.
            processResults(executeStatementRequest(dynamoDB, "UPDATE Music SET BandMembers =set_add(BandMembers, <<'newmember'>>) where Artist=? and SongTitle=?", parameters));

            //Retrieve an item from the Music table using the SELECT PartiQL statement.
            processResults(executeStatementRequest(dynamoDB, "SELECT * FROM Music  where Artist=? and SongTitle=?", parameters));

            //delete an item from the Music Table
            processResults(executeStatementRequest(dynamoDB, "DELETE  FROM Music  where Artist=? and SongTitle=?", parameters));
        } catch (Exception e) {
            handleExecuteStatementErrors(e);
        }
    }

    private static AmazonDynamoDB createDynamoDbClient(String region) {
        return AmazonDynamoDBClientBuilder.standard().withRegion(region).build();
    }

    private static List<AttributeValue> getPartiQLParameters() {
        List<AttributeValue> parameters = new ArrayList<AttributeValue>();
        parameters.add(new AttributeValue("Acme Band"));
        parameters.add(new AttributeValue("PartiQL Rocks"));
        return parameters;
    }

    private static ExecuteStatementResult executeStatementRequest(AmazonDynamoDB client, String statement, List<AttributeValue> parameters ) {
        ExecuteStatementRequest request = new ExecuteStatementRequest();
        request.setStatement(statement);
        request.setParameters(parameters);
        return client.executeStatement(request);
    }

    private static void processResults(ExecuteStatementResult executeStatementResult) {
        System.out.println("ExecuteStatement successful: "+ executeStatementResult.toString());

    }

    // Handles errors during ExecuteStatement execution. Use recommendations in error messages below to add error handling specific to
    // your application use-case.
    private static void handleExecuteStatementErrors(Exception exception) {
        try {
            throw exception;
        } catch (ConditionalCheckFailedException ccfe) {
            System.out.println("Condition check specified in the operation failed, review and update the condition " +
                                       "check before retrying. Error: " + ccfe.getErrorMessage());
        } catch (TransactionConflictException tce) {
            System.out.println("Operation was rejected because there is an ongoing transaction for the item, generally " +
                                       "safe to retry with exponential back-off. Error: " + tce.getErrorMessage());
        } catch (ItemCollectionSizeLimitExceededException icslee) {
            System.out.println("An item collection is too large, you\'re using Local Secondary Index and exceeded " +
                                       "size limit of items per partition key. Consider using Global Secondary Index instead. Error: " + icslee.getErrorMessage());
        } catch (Exception e) {
            handleCommonErrors(e);
        }
    }

    private static void handleCommonErrors(Exception exception) {
        try {
            throw exception;
        } catch (InternalServerErrorException isee) {
            System.out.println("Internal Server Error, generally safe to retry with exponential back-off. Error: " + isee.getErrorMessage());
        } catch (RequestLimitExceededException rlee) {
            System.out.println("Throughput exceeds the current throughput limit for your account, increase account level throughput before " +
                                       "retrying. Error: " + rlee.getErrorMessage());
        } catch (ProvisionedThroughputExceededException ptee) {
            System.out.println("Request rate is too high. If you're using a custom retry strategy make sure to retry with exponential back-off. " +
                                       "Otherwise consider reducing frequency of requests or increasing provisioned capacity for your table or secondary index. Error: " +
                                       ptee.getErrorMessage());
        } catch (ResourceNotFoundException rnfe) {
            System.out.println("One of the tables was not found, verify table exists before retrying. Error: " + rnfe.getErrorMessage());
        } catch (AmazonServiceException ase) {
            System.out.println("An AmazonServiceException occurred, indicates that the request was correctly transmitted to the DynamoDB " +
                                       "service, but for some reason, the service was not able to process it, and returned an error response instead. Investigate and " +
                                       "configure retry strategy. Error type: " + ase.getErrorType() + ". Error message: " + ase.getErrorMessage());
        } catch (AmazonClientException ace) {
            System.out.println("An AmazonClientException occurred, indicates that the client was unable to get a response from DynamoDB " +
                                       "service, or the client was unable to parse the response from the service. Investigate and configure retry strategy. "+
                                       "Error: " + ace.getMessage());
        } catch (Exception e) {
            System.out.println("An exception occurred, investigate and configure retry strategy. Error: " + e.getMessage());
        }
    }

}
```

------