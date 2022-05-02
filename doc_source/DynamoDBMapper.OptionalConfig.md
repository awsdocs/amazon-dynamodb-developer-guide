# Optional Configuration Settings for DynamoDBMapper<a name="DynamoDBMapper.OptionalConfig"></a>

When you create an instance of `DynamoDBMapper`, it has certain default behaviors; you can override these defaults by using the `DynamoDBMapperConfig` class\. 

The following code snippet creates a `DynamoDBMapper` with custom settings:

```
AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard().build();

DynamoDBMapperConfig mapperConfig = DynamoDBMapperConfig.builder()
        .withSaveBehavior(DynamoDBMapperConfig.SaveBehavior.CLOBBER)
        .withConsistentReads(DynamoDBMapperConfig.ConsistentReads.CONSISTENT)
        .withTableNameOverride(null)
        .withPaginationLoadingStrategy(DynamoDBMapperConfig.PaginationLoadingStrategy.EAGER_LOADING)
    .build();

DynamoDBMapper mapper = new DynamoDBMapper(client, mapperConfig);
```

For more information, see [https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/dynamodbv2/datamodeling/DynamoDBMapperConfig.html](https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/dynamodbv2/datamodeling/DynamoDBMapperConfig.html) in the [AWS SDK for Java API Reference](https://docs.aws.amazon.com/sdk-for-java/latest/reference/)\.

You can use the following arguments for an instance of `DynamoDBMapperConfig`:
+ A `DynamoDBMapperConfig.ConsistentReads` enumeration value:
  + `EVENTUAL`—the mapper instance uses an eventually consistent read request\.
  + `CONSISTENT`—the mapper instance uses a strongly consistent read request\. You can use this optional setting with `load`, `query`, or `scan` operations\. Strongly consistent reads have implications for performance and billing; see the DynamoDB [product detail page](https://aws.amazon.com/dynamodb) for more information\.

  If you do not specify a read consistency setting for your mapper instance, the default is `EVENTUAL`\.
+ A `DynamoDBMapperConfig.PaginationLoadingStrategy` enumeration value—Controls how the mapper instance processes a paginated list of data, such as the results from a `query` or `scan`:
  + `LAZY_LOADING`—the mapper instance loads data when possible, and keeps all loaded results in memory\.
  + `EAGER_LOADING`—the mapper instance loads the data as soon as the list is initialized\.
  + `ITERATION_ONLY`—you can only use an Iterator to read from the list\. During the iteration, the list will clear all the previous results before loading the next page, so that the list will keep at most one page of the loaded results in memory\. This also means the list can only be iterated once\. This strategy is recommended when handling large items, in order to reduce memory overhead\.

  If you do not specify a pagination loading strategy for your mapper instance, the default is `LAZY_LOADING`\.
+ A `DynamoDBMapperConfig.SaveBehavior` enumeration value \- Specifies how the mapper instance should deal with attributes during save operations:
  + `UPDATE`—during a save operation, all modeled attributes are updated, and unmodeled attributes are unaffected\. Primitive number types \(byte, int, long\) are set to 0\. Object types are set to null\. 
  + `CLOBBER`—clears and replaces all attributes, included unmodeled ones, during a save operation\. This is done by deleting the item and re\-creating it\. Versioned field constraints are also disregarded\.

   If you do not specify the save behavior for your mapper instance, the default is `UPDATE`\.
**Note**  
DynamoDBMapper transactional operations do not support `DynamoDBMapperConfig.SaveBehavior` enumeration\. 
+ A `DynamoDBMapperConfig.TableNameOverride` object—Instructs the mapper instance to ignore the table name specified by a class's `DynamoDBTable` annotation, and instead use a different table name that you supply\. This is useful when partitioning your data into multiple tables at runtime\. 

You can override the default configuration object for `DynamoDBMapper` per operation, as needed\.