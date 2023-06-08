# Best practices for using secondary indexes in DynamoDB<a name="bp-indexes"></a>

Secondary indexes are often essential to support the query patterns that your application requires\. At the same time, overusing secondary indexes or using them inefficiently can add cost and reduce performance unnecessarily\.

**Contents**
+ [General guidelines for secondary indexes in DynamoDB](bp-indexes-general.md)
  + [Use indexes efficiently](bp-indexes-general.md#bp-indexes-general-efficiency)
  + [Choose projections carefully](bp-indexes-general.md#bp-indexes-general-projections)
  + [Optimize frequent queries to avoid fetches](bp-indexes-general.md#bp-indexes-general-fetches)
  + [Be aware of item\-collection size limits when creating local secondary indexes](bp-indexes-general.md#bp-indexes-general-expanding-collections)
+ [Take advantage of sparse indexes](bp-indexes-general-sparse-indexes.md)
  + [Examples of sparse indexes in DynamoDB](bp-indexes-general-sparse-indexes.md#bp-indexes-sparse-examples)
+ [Using Global Secondary Indexes for materialized aggregation queries](bp-gsi-aggregation.md)
+ [Overloading Global Secondary Indexes](bp-gsi-overloading.md)
+ [Using Global Secondary Index write sharding for selective table queries](bp-indexes-gsi-sharding.md)
+ [Using Global Secondary Indexes to create an eventually consistent replica](bp-indexes-gsi-replica.md)