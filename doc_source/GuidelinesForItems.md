# Best Practices for Items<a name="GuidelinesForItems"></a>


+ [Use One\-to\-Many Tables Instead Of Large Set Attributes](#GuidelinesForItems.OneToMany)
+ [Compress Large Attribute Values](#GuidelinesForItems.CompressingLargeAttributeValues)
+ [Store Large Attribute Values in Amazon S3](#GuidelinesForItems.StoringInS3)
+ [Break Up Large Attributes Across Multiple Items](#GuidelinesForItems.BreakingUpLargeAttribes)

When you are working with items in DynamoDB, you need to consider how to get the best performance, how to reduce provisioned throughput costs, and how to avoid throttling by staying within your read and write capacity units\. If the items that you are handling exceed the maximum item size, as described in [Limits in DynamoDB](Limits.md), you need to consider how you will deal with the situation\. This section offers best practices for addressing these considerations\. 

## Use One\-to\-Many Tables Instead Of Large Set Attributes<a name="GuidelinesForItems.OneToMany"></a>

If your table has items that store a large set type attribute, such as number set or string set, consider removing the attribute and breaking the table into two tables\. To form one\-to\-many relationships between these tables, use the primary keys\.

The Forum, Thread, and Reply tables in the [Creating Tables and Loading Sample Data](SampleData.md) section are good examples of these one\-to\-many relationships\. For example, the Thread table has one item for each forum thread, and the Reply table stores one or more replies for each thread\.

Instead of storing replies as items in a separate table, you could store both threads and replies in the same table\. For each thread, you could store all replies in an attribute of string set type; however, keeping thread and reply data in separate tables is beneficial in several ways:

+ If you store replies as items in a table, you can store any number of replies, because a DynamoDB table can store any number of items\.

  If you store replies as an attribute value in the Thread table, you would be constrained by the maximum item size, which would limit the number of replies that you could store\. \(See [Limits in DynamoDB](Limits.md)\)

+ When you retrieve a Thread item, you pay less for provisioned throughput, because you are retrieving only the thread data and not all the replies for that thread\.

+ Queries allow you to retrieve only a subset of items from a table\. By storing replies in a separate Reply table, you can retrieve only a subset of replies, for example, those within a specific date range, by querying the Reply table\.

  If you store replies as a set type attribute value, you would always have to retrieve all the replies, which would consume more provisioned throughput for data that you might not need\. 

+ When you add a new reply to a thread, you add only an item to the Reply table, which incurs the provisioned throughput cost of only that single Reply item\. If replies are stored in the Thread table, you incur the full cost of writing the entire Thread item including all replies whenever you add a single user reply to a thread\.

## Compress Large Attribute Values<a name="GuidelinesForItems.CompressingLargeAttributeValues"></a>

You can compress large values before storing them in DynamoDB\. Doing so can reduce the cost of storing and retrieving such data\. Compression algorithms, such as GZIP or LZO, produce a binary output\. You can then store this output in a Binary attribute type\.

 For example, the Reply table in the [Creating Tables and Loading Sample Data](SampleData.md) section stores messages written by forum users\. These user replies might consist of very long strings of text, which makes them excellent candidates for compression\.

For sample code that demonstrates how to compress long messages in DynamoDB, see:

+ [Example: Handling Binary Type Attributes Using the AWS SDK for Java Document API](JavaDocumentAPIBinaryTypeExample.md)

+ [Example: Handling Binary Type Attributes Using the AWS SDK for \.NET Low\-Level API](LowLevelDotNetBinaryTypeExample.md)

## Store Large Attribute Values in Amazon S3<a name="GuidelinesForItems.StoringInS3"></a>

DynamoDB currently limits the size of the items that you store in tables\. For more information, see [Limits in DynamoDB](Limits.md)\. Your application, however, might need to store more data in an item than the DynamoDB size limits permit\. To work around this issue, you can store the large attributes as an object in Amazon Simple Storage Service \(Amazon S3\), and store the object identifier in your item\. You can also use the object metadata support in Amazon S3 to store the primary key value of the corresponding item as Amazon S3 object metadata\. This use of metadata can help with future maintenance of your Amazon S3 objects\.

For example, consider the *ProductCatalog* table in the [Creating Tables and Loading Sample Data](SampleData.md) section\. Items in the *ProductCatalog* table store information about item price, description, authors for books, and dimensions for other products\. If you wanted to store an image of each product, these images could be large\. It might make sense to store the images in Amazon S3 instead of DynamoDB\.

There are important considerations with this approach:

+ Since DynamoDB does not support transactions that cross Amazon S3 and DynamoDB, your application will have to deal with failures and with cleaning up orphaned Amazon S3 objects\.

+ Amazon S3 limits length of object identifiers, so you must organize your data in a way that accommodates this and other Amazon S3 constraints\. For more information, see the [Amazon Simple Storage Service Developer Guide](http://docs.aws.amazon.com/AmazonS3/latest/dev/)\.

## Break Up Large Attributes Across Multiple Items<a name="GuidelinesForItems.BreakingUpLargeAttribes"></a>

If you need to store more data in a single item than DynamoDB allows, you can store that data in multiple items as chunks of a larger "virtual item"\. To get the best results, store the chunks in a separate table that has a simple primary key \(partition key\), and use batch operations \(`BatchGetItem` and `BatchWriteItem`\) to read and write the chunks\. This approach will help you to spread your workload evenly across table partitions\.

For example, consider the *Forum*, *Thread* and *Reply* tables described in the [Creating Tables and Loading Sample Data](SampleData.md) section\. Items in the *Reply* table contain forum messages that were written by forum users\. Due to the 400 KB item size limit in DynamoDB, the length of each reply is also limited\. For large replies, instead of storing one item in the *Reply* table, break the reply message into chunks, and then write each chunk into its own separate item in a new *ReplyChunks* table with a simple primary key \(partition key\)\.

The primary key of each chunk would be a concatenation of the primary key of its "parent" reply item, a version number, and a sequence number\. The sequence number determines the order of the chunks\. The version number ensures that if a large reply is updated later, it will be updated atomically\. In addition, chunks that were created before the update will not be mixed with chunks that were created after the update\. 

You would also need to update the "parent" reply item with the number of chunks, so that when you need to retrieve all the chunks for a reply, you will know how many chunks to look for\.

As an illustration, here is how these items might appear in the *Reply* and *ReplyChunks* tables:

**Reply**


****  

| *Id* | *ReplyDateTime* | Message | ChunkCount | ChunkVersion | 
| --- | --- | --- | --- | --- | 
| "DynamoDB\#Thread1" | "2012\-03\-15T20:42:54\.023Z" |  | 3 | 1 | 
| "DynamoDB\#Thread2" | "2012\-03\-21T20:41:23\.192Z" | "short message" |  |  | 

**ReplyChunks**


****  

| *Id* | Message | 
| --- | --- | 
| "DynamoDB\#Thread1\#2012\-03\-15T20:42:54\.023Z\#1\#1" | "first part of long message text\.\.\." | 
| "DynamoDB\#Thread1\#2012\-03\-15T20:42:54\.023Z\#1\#3" | "\.\.\.third part of long message text" | 
| "DynamoDB\#Thread1\#2012\-03\-15T20:42:54\.023Z\#1\#2" | "\.\.\.second part of long message text\.\.\." | 

Here are important considerations with this approach:

+ Because DynamoDB does not support cross\-item transactions, your application will need to deal with failure scenarios when writing multiple items and with inconsistencies between items when reading multiple items\.

+ If your application retrieves a large amount of data all at once, it can generate nonuniform workloads, which can cause unexpected throttling\. This is especially true when retrieving items that share a partition key value\.

Chunking large data items avoids this problem by using a separate table with a simple primary key \(partition key\), so that each large chunk is spread across the table\.

A workable, but less optimal, solution would be to store each chunk in a table with a composite key, with the partition key being the primary key of the "parent" item\. With this design choice, an application that retrieves all of the chunks of the same "parent" item would generate a nonuniform workload, with uneven I/O distribution across partitions\.