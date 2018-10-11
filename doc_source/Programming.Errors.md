# Error Handling<a name="Programming.Errors"></a>

 This section describes runtime errors and how to handle them\. It also describes error messages and codes that are specific to DynamoDB\.

**Topics**
+ [Error Components](#Programming.Errors.Components)
+ [Error Messages and Codes](#Programming.Errors.MessagesAndCodes)
+ [Error Handling in Your Application](#Programming.Errors.Handling)
+ [Error Retries and Exponential Backoff](#Programming.Errors.RetryAndBackoff)
+ [Batch Operations and Error Handling](#Programming.Errors.BatchOperations)

## Error Components<a name="Programming.Errors.Components"></a>

When your program sends a request, DynamoDB attempts to process it\. If the request is successful, DynamoDB returns an HTTP success status code \(`200 OK`\), along with the results from the requested operation\.

If the request is unsuccessful, DynamoDB returns an error\. Each error has three components: 
+ An HTTP status code \(such as `400`\)\.
+ An exception name \(such as `ResourceNotFoundException`\)\.
+ An error message \(such as `Requested resource not found: Table: tablename not found`\)\.

The AWS SDKs take care of propagating errors to your application, so that you can take appropriate action\. For example, in a Java program, you can write `try-catch` logic to handle a `ResourceNotFoundException`\.

If you are not using an AWS SDK, you will need to parse the content of the low\-level response from DynamoDB\. The following is an example of such a response:

```
HTTP/1.1 400 Bad Request
x-amzn-RequestId: LDM6CJP8RMQ1FHKSC1RBVJFPNVV4KQNSO5AEMF66Q9ASUAAJG
Content-Type: application/x-amz-json-1.0
Content-Length: 240
Date: Thu, 15 Mar 2012 23:56:23 GMT

{"__type":"com.amazonaws.dynamodb.v20120810#ResourceNotFoundException",
"message":"Requested resource not found: Table: tablename not found"}
```

## Error Messages and Codes<a name="Programming.Errors.MessagesAndCodes"></a>

The following is a list of exceptions returned by DynamoDB, grouped by HTTP status code\. If *OK to retry?* is *Yes*, you can submit the same request again\. If *OK to retry?* is *No*, you will need to fix the problem on the client side before you submit a new request\.

### HTTP Status Code 400<a name="w3ab1c15c15b9b5"></a>

An HTTP `400` status code indicates a problem with your request, such as authentication failure, missing required parameters, or exceeding a table's provisioned throughput\. You will have to fix the issue in your application before submitting the request again\.

**AccessDeniedException **  
Message: *Access denied\.*  
The client did not correctly sign the request\. If you are using an AWS SDK, requests are signed for you automatically; otherwise, go to the [Signature Version 4 Signing Process](http://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) in the *AWS General Reference*\.  
OK to retry? No

**ConditionalCheckFailedException**  
Message: *The conditional request failed\. *  
You specified a condition that evaluated to false\. For example, you might have tried to perform a conditional update on an item, but the actual value of the attribute did not match the expected value in the condition\.  
OK to retry? No

**IncompleteSignatureException**  
Message: *The request signature does not conform to AWS standards\. *  
The request signature did not include all of the required components\. If you are using an AWS SDK, requests are signed for you automatically; otherwise, go to the [Signature Version 4 Signing Process](http://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) in the *AWS General Reference*\.  
OK to retry? No

**ItemCollectionSizeLimitExceededException**  
Message: *Collection size exceeded\.*  
For a table with a local secondary index, a group of items with the same partition key value has exceeded the maximum size limit of 10 GB\. For more information on item collections, see [Item Collections](LSI.md#LSI.ItemCollections)\.  
OK to retry? Yes

**LimitExceededException**  
Message: *Too many operations for a given subscriber\.*  
There are too many concurrent control plane operations\. The cumulative number of tables and indexes in the `CREATING`, `DELETING` or `UPDATING` state cannot exceed 10\.  
OK to retry? Yes

**MissingAuthenticationTokenException**  
Message: *Request must contain a valid \(registered\) AWS Access Key ID\.*  
The request did not include the required authorization header, or it was malformed\. See [DynamoDB Low\-Level API](Programming.LowLevelAPI.md)\.  
OK to retry? No

**ProvisionedThroughputExceededException**  
Message: *You exceeded your maximum allowed provisioned throughput for a table or for one or more global secondary indexes\. To view performance metrics for provisioned throughput vs\. consumed throughput, open the [Amazon CloudWatch console](https://console.aws.amazon.com/cloudwatch/home)\.*  
Example: Your request rate is too high\. The AWS SDKs for DynamoDB automatically retry requests that receive this exception\. Your request is eventually successful, unless your retry queue is too large to finish\. Reduce the frequency of requests, using [Error Retries and Exponential Backoff](#Programming.Errors.RetryAndBackoff)\.   
OK to retry? Yes

**ResourceInUseException**  
Message: *The resource which you are attempting to change is in use\. *  
Example: You tried to recreate an existing table, or delete a table currently in the `CREATING` state\.   
OK to retry? No

**ResourceNotFoundException **  
Message: *Requested resource not found\.*  
Example: Table which is being requested does not exist, or is too early in the `CREATING` state\.  
OK to retry? No

**ThrottlingException**  
Message: *Rate of requests exceeds the allowed throughput\.*  
This exception might be returned if you perform any of the following operations too rapidly: `CreateTable`; `UpdateTable`; `DeleteTable`\.  
OK to retry? Yes

**UnrecognizedClientException**  
Message: *The Access Key ID or security token is invalid\.*  
The request signature is incorrect\. The most likely cause is an invalid AWS access key ID or secret key\.  
OK to retry? Yes

**ValidationException**  
Message: Varies, depending upon the specific error\(s\) encountered  
This error can occur for several reasons, such as a required parameter that is missing, a value that is out range, or mismatched data types\. The error message contains details about the specific part of the request that caused the error\.  
OK to retry? No

### HTTP Status Code 5xx<a name="w3ab1c15c15b9b7"></a>

An HTTP `5xx` status code indicates a problem that must be resolved by Amazon Web Services\. This might be a transient error in which case you can retry your request until it succeeds\. Otherwise, go to the [AWS Service Health Dashboard](http://status.aws.amazon.com/) to see if there are any operational issues with the service\.

**Internal Server Error \(HTTP 500\)**  
DynamoDB could not process your request\.  
OK to retry? Yes  
You may encounter Internal Server Errors while working with items\. These are expected during the lifetime of a table\. Any failed requests can be retried immediately\.

**Service Unavailable \(HTTP 503\)**  
DynamoDB is currently unavailable\. \(This should be a temporary state\.\)  
OK to retry? Yes

## Error Handling in Your Application<a name="Programming.Errors.Handling"></a>

For your application to run smoothly, you will need to add logic to catch and respond to errors\. Typical approaches include using `try-catch` blocks or an `if-then` statements\.

The AWS SDKs perform their own retries and error checking\. If you encounter an error while using one of the AWS SDKs, the error code and description can help you troubleshoot it\. 

You should also see a `Request ID` in the response\. The `Request ID` can be helpful if you need to work with AWS Support to diagnose an issue\.

The following Java code snippet attempts to delete an item from a DynamoDB table, and performs rudimentary error handling\. \(In this case, it simply informs the user that the request failed\)\. 

```
Table table = dynamoDB.getTable("Movies");

try {
    Item item = table.getItem("year", 1978, "title", "Superman");
    if (item != null) {
        System.out.println("Result: " + item);
    } else { 
        //No such item exists in the table
        System.out.println("Item not found");
    }

} catch (AmazonServiceException ase) {
    System.err.println("Could not complete operation");
    System.err.println("Error Message:  " + ase.getMessage());
    System.err.println("HTTP Status:    " + ase.getStatusCode());
    System.err.println("AWS Error Code: " + ase.getErrorCode());
    System.err.println("Error Type:     " + ase.getErrorType());
    System.err.println("Request ID:     " + ase.getRequestId());

} catch (AmazonClientException ace) {
    System.err.println("Internal error occured communicating with DynamoDB");
    System.out.println("Error Message:  " + ace.getMessage());
}
```

In this code snippet, the `try-catch` construct handles two different kinds of exceptions:
+ `AmazonServiceException`—thrown if the client request was correctly transmitted to DynamoDB, but DynamoDB was unable to process the request and returned an error response instead\.
+ `AmazonClientException`—thrown if the client was unable to get a response from a service, or if the client was unable to parse the response from a service\.

## Error Retries and Exponential Backoff<a name="Programming.Errors.RetryAndBackoff"></a>

Numerous components on a network, such as DNS servers, switches, load balancers, and others can generate errors anywhere in the life of a given request\. The usual technique for dealing with these error responses in a networked environment is to implement retries in the client application\. This technique increases the reliability of the application\.

Each AWS SDK implements retry logic, automatically\. You can modify the retry parameters to your needs\. For example, consider a Java application that requires a fail\-fast strategy, with no retries allowed in case of an error\. With the AWS SDK for Java, you could use the `ClientConfiguration` class and provide a `maxErrorRetry` value of `0` to turn off the retries\. For more information, see the AWS SDK documentation for your programming language

If you're not using an AWS SDK, you should retry original requests that receive server errors \(5xx\)\. However, client errors \(4xx, other than a `ThrottlingException` or a `ProvisionedThroughputExceededException`\) indicate you need to revise the request itself to correct the problem before trying again\. 

In addition to simple retries, each AWS SDK implements exponential backoff algorithm for better flow control\. The concept behind exponential backoff is to use progressively longer waits between retries for consecutive error responses\. For example, up to 50 milliseconds before the first retry, up to 100 milliseconds before the second, up to 200 milliseconds before third, and so on\. However, after a minute, if the request has not succeeded, the problem might be the request size exceeding your provisioned throughput, and not the request rate\. Set the maximum number of retries to stop around one minute\. If the request is not successful, investigate your provisioned throughput options\. 

**Note**  
The AWS SDKs implement automatic retry logic and exponential backoff\.

Most exponential backoff algorithms use jitter \(randomized delay\) to prevent successive collisions\. Because you aren't trying to avoid such collisions in these cases, you do not need to use this random number\. However, if you use concurrent clients, jitter can help your requests succeed faster\. For more information, see the blog post for [Exponential Backoff and Jitter](http://www.awsarchitectureblog.com/2015/03/backoff.html)\.

## Batch Operations and Error Handling<a name="Programming.Errors.BatchOperations"></a>

The DynamoDB low\-level API supports batch operations for reads and writes\. `BatchGetItem` reads items from one or more tables, and `BatchWriteItem` puts or deletes items in one or more tables\. These batch operations are implemented as wrappers around other non\-batch DynamoDB operations\. In other words, `BatchGetItem` invokes `GetItem` once for each item in the batch\. Similarly,`BatchWriteItem` invokes `DeleteItem` or `PutItem`, as appropriate, for each item in the batch\.

A batch operation can tolerate the failure of individual requests in the batch\. For example, consider a `BatchGetItem` request to read five items\. Even if some of the underlying `GetItem` requests fail, this will not cause the entire `BatchGetItem` operation to fail\. On the other hand, if *all* of the five reads operations fail, then the entire `BatchGetItem` will fail\.

The batch operations return information about individual requests that fail, so that you can diagnose the problem and retry the operation\. For `BatchGetItem`, the tables and primary keys in question are returned in the `UnprocessedKeys` parameter of the request\. For `BatchWriteItem`, similar information is returned in `UnprocessedItems`\. 

The most likely cause of a failed read or a failed write is *throttling*\. For `BatchGetItem`, one or more of the tables in the batch request does not have enough provisioned read capacity to support the operation\. For `BatchWriteItem`, one or more of the tables does not have enough provisioned write capacity\.

If DynamoDB returns any unprocessed items, you should retry the batch operation on those items\. However, *we strongly recommend that you use an exponential backoff algorithm*\. If you retry the batch operation immediately, the underlying read or write requests can still fail due to throttling on the individual tables\. If you delay the batch operation using exponential backoff, the individual requests in the batch are much more likely to succeed\.
