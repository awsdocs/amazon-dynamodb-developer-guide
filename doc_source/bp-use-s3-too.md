# Best Practices for Storing Large Items and Attributes<a name="bp-use-s3-too"></a>

Amazon DynamoDB currently limits the size of each item that you store in a table \(see [Service, Account, and Table Quotas in Amazon DynamoDB](ServiceQuotas.md)\)\. If your application needs to store more data in an item than the DynamoDB size limit permits, you can try compressing one or more large attributes or breaking the item into multiple items \(efficiently indexed by sort keys\)\. You can also store the item as an object in Amazon Simple Storage Service \(Amazon S3\) and store the Amazon S3 object identifier in your DynamoDB item\.

## Compressing Large Attribute Values<a name="bp-use-s3-too-or-compress"></a>

Compressing large attribute values can let them fit within item limits in DynamoDB and reduce your storage costs\. Compression algorithms such as GZIP or LZO produce binary output that you can then store in a `Binary` attribute type\.

For example, the `Reply` table in the [Creating Tables and Loading Data for Code Examples in DynamoDB](SampleData.md) section stores messages written by forum users\. These user replies might consist of long strings of text, which makes them excellent candidates for compression\.

For sample code that demonstrates how to compress such messages in DynamoDB, see the following:
+ [Example: Handling Binary Type Attributes Using the AWS SDK for Java Document API](JavaDocumentAPIBinaryTypeExample.md)
+ [Example: Handling Binary Type Attributes Using the AWS SDK for \.NET Low\-Level API](LowLevelDotNetBinaryTypeExample.md)

## Storing Large Attribute Values in Amazon S3<a name="bp-use-s3-too-large-values"></a>

As mentioned previously, you can also use Amazon S3 to store large attribute values that cannot fit in a DynamoDB item\. You can store them as an object in Amazon S3 and then store the object identifier in your DynamoDB item\.

You can also use the object metadata support in Amazon S3 to provide a link back to the parent item in DynamoDB\. Store the primary key value of the item as Amazon S3 metadata of the object in Amazon S3\. Doing this often helps with maintenance of the Amazon S3 objects\.

For example, consider the `ProductCatalog` table in the [Creating Tables and Loading Data for Code Examples in DynamoDB](SampleData.md) section\. Items in this table store information about item price, description, book authors, and dimensions for other products\. If you wanted to store an image of each product that was too large to fit in an item, you could store the images in Amazon S3 instead of in DynamoDB\.

When implementing this strategy, keep the following in mind:
+ DynamoDB doesn't support transactions that cross Amazon S3 and DynamoDB\. Therefore, your application must deal with any failures, which could include cleaning up orphaned Amazon S3 objects\.
+ Amazon S3 limits the length of object identifiers\. So you must organize your data in a way that doesn't generate excessively long object identifiers or violate other Amazon S3 constraints\.

For more information about how to use Amazon S3, see the [Amazon Simple Storage Service User Guide](https://docs.aws.amazon.com/AmazonS3/latest/dev/)\.