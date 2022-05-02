# Running Batch Operations with PartiQL for DynamoDB<a name="ql-reference.multiplestatements.batching"></a>

This section describes how to use batch statements with PartiQL for DynamoDB\.

**Note**  
The entire batch must consist of either read statements or write statements; you cannot mix both in one batch\.

**Topics**
+ [Syntax](#ql-reference.multiplestatements.batching.syntax)
+ [Parameters](#ql-reference.multiplestatements.batching.parameters)
+ [Examples](#ql-reference.multiplestatements.batching.examples)

## Syntax<a name="ql-reference.multiplestatements.batching.syntax"></a>

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

## Parameters<a name="ql-reference.multiplestatements.batching.parameters"></a>

***statement***  
\(Required\) A PartiQL for DynamoDB supported statement\.  
The entire batch must consist of either read statements or write statements; you cannot mix both in one batch\.

***parametertype***  
\(Optional\) A DynamoDB type, if parameters were used when specifying the PartiQL statement\.

***parametervalue***  
\(Optional\) A parameter value if parameters were used when specifying the PartiQL statement\.

## Examples<a name="ql-reference.multiplestatements.batching.examples"></a>

------
#### [ AWS CLI ]

1. Save the following json to a file called partiql\.json

   ```
   [    
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
   aws dynamodb batch-execute-statement  --statements  file://partiql.json
   ```

------
#### [ Java ]

```
public class DynamoDBPartiqlBatch {

    public static void main(String[] args) {
        // Create the DynamoDB Client with the region you want
        AmazonDynamoDB dynamoDB = createDynamoDbClient("us-west-2");
        
        try {
            // Create BatchExecuteStatementRequest
            BatchExecuteStatementRequest batchExecuteStatementRequest = createBatchExecuteStatementRequest();
            BatchExecuteStatementResult batchExecuteStatementResult = dynamoDB.batchExecuteStatement(batchExecuteStatementRequest);
            System.out.println("BatchExecuteStatement successful.");
            // Handle batchExecuteStatementResult

        } catch (Exception e) {
            handleBatchExecuteStatementErrors(e);
        }
    }

    private static AmazonDynamoDB createDynamoDbClient(String region) {

        return AmazonDynamoDBClientBuilder.standard().withRegion(region).build();
    }

    private static BatchExecuteStatementRequest createBatchExecuteStatementRequest() {
        BatchExecuteStatementRequest request = new BatchExecuteStatementRequest();

        // Create statements
        List<BatchStatementRequest> statements = getPartiQLBatchStatements();

        request.setStatements(statements);
        return request;
    }

    private static List<BatchStatementRequest> getPartiQLBatchStatements() {
        List<BatchStatementRequest> statements = new ArrayList<BatchStatementRequest>();

        statements.add(new BatchStatementRequest()
                               .withStatement("INSERT INTO "Music" value {'Artist':'Acme Band','SongTitle':'PartiQL Rocks'}"));

        statements.add(new BatchStatementRequest()
                               .withStatement("UPDATE "Music" set AwardDetail.BillBoard=[2020] where Artist='Acme Band' and SongTitle='PartiQL Rocks'"));

        return statements;
    }

    // Handles errors during BatchExecuteStatement execution. Use recommendations in error messages below to add error handling specific to 
    // your application use-case.
    private static void handleBatchExecuteStatementErrors(Exception exception) {
        try {
            throw exception;
        } catch (Exception e) {
            // There are no API specific errors to handle for BatchExecuteStatement, common DynamoDB API errors are handled below
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