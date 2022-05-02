# Performing Transactions with PartiQL for DynamoDB<a name="ql-reference.multiplestatements.transactions"></a>

This section describes how to use transactions with PartiQL for DynamoDB\.

For more information on DynamoDB transactions, see [Managing Complex Workflows with DynamoDB Transactions](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/transactions.html)\.

**Note**  
The entire transaction must consist of either read statements or write statements\. You can't mix both in one transaction\. The EXISTS function is an exception\. You can use it to check the condition of specific attributes of the item in a similar manner to `ConditionCheck` in the [TransactWriteItems](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/transaction-apis.html#transaction-apis-txwriteitems) API operation\.

**Topics**
+ [Syntax](#ql-reference.multiplestatements.transactions.syntax)
+ [Parameters](#ql-reference.multiplestatements.transactions.parameters)
+ [Return Values](#ql-reference.multiplestatements.transactions.return)
+ [Examples](#ql-reference.multiplestatements.transactions.examples)

## Syntax<a name="ql-reference.multiplestatements.transactions.syntax"></a>

```
[
   {
      "Statement":" statement ",
      "Parameters":[
         {
            " parametertype " : " parametervalue "
         }, ...]
   } , ...
]
```

## Parameters<a name="ql-reference.multiplestatements.transactions.parameters"></a>

***statement***  
\(Required\) A PartiQL for DynamoDB supported statement\.  
The entire transaction must consist of either read statements or write statements\. You can't mix both in one transaction\.

***parametertype***  
\(Optional\) A DynamoDB type, if parameters were used when specifying the PartiQL statement\.

***parametervalue***  
\(Optional\) A parameter value if parameters were used when specifying the PartiQL statement\.

## Return Values<a name="ql-reference.multiplestatements.transactions.return"></a>

This statement doesn't return any values for Write operations \(INSERT, UPDATE, or DELETE\)\. However, it returns different values for Read operations \(SELECT\) based on the conditions specified in the WHERE clause\.

**Note**  
If any of the singleton INSERT, UPDATE, or DELETE operations return an error, the transactions are canceled with the `TransactionCanceledException` exception, and the cancellation reason code includes the errors from the individual singleton operations\.

## Examples<a name="ql-reference.multiplestatements.transactions.examples"></a>

The following example runs multiple statements as a transaction\.

------
#### [ AWS CLI ]

1. Save the following JSON code to a file called partiql\.json\.

   ```
   [
       {
           "Statement": "EXISTS(SELECT * FROM "Music" where Artist='No One You Know' and SongTitle='Call Me Today' and Awards is  MISSING)"
       },
       {
           "Statement": "INSERT INTO "Music" value {'Artist':'?','SongTitle':'?'}",
           "Parameters": [{"S": "Acme Band"}, {"S": "Best Song"}]
       },
       {
           "Statement": "UPDATE "Music" SET AwardsWon=1 SET AwardDetail={'Grammys':[2020, 2018]}  where Artist='Acme Band' and SongTitle='PartiQL Rocks'"
       }
   ]
   ```

1. Run the following command in a command prompt\.

   ```
   aws dynamodb execute-transaction --transact-statements  file://partiql.json
   ```

------
#### [ Java ]

```
public class DynamoDBPartiqlTransaction {

    public static void main(String[] args) {
        // Create the DynamoDB Client with the region you want
        AmazonDynamoDB dynamoDB = createDynamoDbClient("us-west-2");
        
        try {
            // Create ExecuteTransactionRequest
            ExecuteTransactionRequest executeTransactionRequest = createExecuteTransactionRequest();
            ExecuteTransactionResult executeTransactionResult = dynamoDB.executeTransaction(executeTransactionRequest);
            System.out.println("ExecuteTransaction successful.");
            // Handle executeTransactionResult

        } catch (Exception e) {
            handleExecuteTransactionErrors(e);
        }
    }

    private static AmazonDynamoDB createDynamoDbClient(String region) {
        return AmazonDynamoDBClientBuilder.standard().withRegion(region).build();
    }

    private static ExecuteTransactionRequest createExecuteTransactionRequest() {
        ExecuteTransactionRequest request = new ExecuteTransactionRequest();
        
        // Create statements
        List<ParameterizedStatement> statements = getPartiQLTransactionStatements();

        request.setTransactStatements(statements);
        return request;
    }

    private static List<ParameterizedStatement> getPartiQLTransactionStatements() {
        List<ParameterizedStatement> statements = new ArrayList<ParameterizedStatement>();

        statements.add(new ParameterizedStatement()
                               .withStatement("EXISTS(SELECT * FROM "Music" where Artist='No One You Know' and SongTitle='Call Me Today' and Awards is  MISSING)"));

        statements.add(new ParameterizedStatement()
                               .withStatement("INSERT INTO "Music" value {'Artist':'?','SongTitle':'?'}")
                               .withParameters(new AttributeValue("Acme Band"),new AttributeValue("Best Song")));

        statements.add(new ParameterizedStatement()
                               .withStatement("UPDATE "Music" SET AwardsWon=1 SET AwardDetail={'Grammys':[2020, 2018]}  where Artist='Acme Band' and SongTitle='PartiQL Rocks'"));

        return statements;
    }

    // Handles errors during ExecuteTransaction execution. Use recommendations in error messages below to add error handling specific to 
    // your application use-case.
    private static void handleExecuteTransactionErrors(Exception exception) {
        try {
            throw exception;
        } catch (TransactionCanceledException tce) {
            System.out.println("Transaction Cancelled, implies a client issue, fix before retrying. Error: " + tce.getErrorMessage());
        } catch (TransactionInProgressException tipe) {
            System.out.println("The transaction with the given request token is already in progress, consider changing " +
                "retry strategy for this type of error. Error: " + tipe.getErrorMessage());
        } catch (IdempotentParameterMismatchException ipme) {
            System.out.println("Request rejected because it was retried with a different payload but with a request token that was already used, " +
                "change request token for this payload to be accepted. Error: " + ipme.getErrorMessage());
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

The following example shows the different return values when DynamoDB reads items with different conditions specified in the WHERE clause\.

------
#### [ AWS CLI ]

1. Save the following JSON code to a file called partiql\.json\.

   ```
   [
       // Item exists and projected attribute exists
       {
           "Statement": "SELECT * FROM "Music" WHERE Artist='No One You Know' and SongTitle='Call Me Today'"
       },
       // Item exists but projected attributes do not exist
       {
           "Statement": "SELECT non_existent_projected_attribute FROM "Music" WHERE Artist='No One You Know' and SongTitle='Call Me Today'"
       },
       // Item does not exist
       {
           "Statement": "SELECT * FROM "Music" WHERE Artist='No One I Know' and SongTitle='Call You Today'"
       }
   ]
   ```

1.  following command in a command prompt\.

   ```
   aws dynamodb execute-transaction --transact-statements  file://partiql.json
   ```

1. The following response is returned:

   ```
   {
       "Responses": [
           // Item exists and projected attribute exists
           {
               "Item": {
                   "Artist":{
                       "S": "No One You Know"
                   },
                   "SongTitle":{
                       "S": "Call Me Today"
                   }    
               }
           },
           // Item exists but projected attributes do not exist
           {
               "Item": {}
           },
           // Item does not exist
           {}
       ]
   }
   ```

------