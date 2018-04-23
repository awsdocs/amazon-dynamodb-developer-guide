# Setting the Local Endpoint<a name="DynamoDBLocal.Endpoint"></a>

By default, the AWS SDKs and tools use endpoints for the Amazon DynamoDB web service\. To use the SDKs and tools with the downloadable version of DynamoDB, you must specify the local endpoint:

`http://localhost:8000`

## AWS Command Line Interface<a name="w3ab1b9b9c11b7"></a>

You can use the AWS Command Line Interface to interact with downloadable DynamoDB\. For example, you can use it to perform all the steps in [Creating Tables and Loading Sample Data](SampleData.md)\.

To access DynamoDB running locally, use the `--endpoint-url` parameter\. The following is an example of using the AWS CLI to list the tables in DynamoDB on your computer:

```
aws dynamodb list-tables --endpoint-url http://localhost:8000
```

**Note**  
The AWS CLI can't use the downloadable version of DynamoDB as a default endpoint; therefore, you must specify `--endpoint-url` with each AWS CLI command\.

## AWS SDKs<a name="w3ab1b9b9c11b9"></a>

The way you specify an endpoint depends on the programming language and AWS SDK you're using\. The following sections describe how to do this:
+ [Java: Setting the AWS Region and Endpoint](CodeSamples.Java.md#CodeSamples.Java.RegionAndEndpoint)
+ [\.NET: Setting the AWS Region and Endpoint](CodeSamples.DotNet.md#CodeSamples.DotNet.RegionAndEndpoint)

**Note**  
For examples in other programming languages, see [Getting Started with DynamoDB](GettingStarted.md)\.