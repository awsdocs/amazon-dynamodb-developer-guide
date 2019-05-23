# Best Practices for Using Secondary Indexes in DynamoDB<a name="bp-indexes"></a>

Secondary indexes are often essential to support the query patterns that your application requires\. At the same time, overusing secondary indexes or using them inefficiently can add cost and reduce performance unnecessarily\.

**Contents**
+ [General Guidelines for Secondary Indexes in DynamoDB](bp-indexes-general.md)
  + [Use Indexes Efficiently](bp-indexes-general.md#bp-indexes-general-efficiency)
  + [Choose Projections Carefully](bp-indexes-general.md#bp-indexes-general-projections)
  + [Optimize Frequent Queries to Avoid Fetches](bp-indexes-general.md#bp-indexes-general-fetches)
  + [Be Aware of Item\-Collection Size Limits When Creating Local Secondary Indexes](bp-indexes-general.md#bp-indexes-general-expanding-collections)
+ [Take Advantage of Sparse Indexes](bp-indexes-general-sparse-indexes.md)
  + [Examples of Sparse Indexes in DynamoDB](bp-indexes-general-sparse-indexes.md#bp-indexes-sparse-examples)
+ [Using Global Secondary Indexes for Materialized Aggregation Queries](bp-gsi-aggregation.md)
+ [Overloading Global Secondary Indexes](bp-gsi-overloading.md)
+ [Using Global Secondary Index Write Sharding for Selective Table Queries](bp-indexes-gsi-sharding.md)
+ [Using Global Secondary Indexes to Create an Eventually Consistent Replica](bp-indexes-gsi-replica.md)