# Designing Partition Keys to Distribute Your Workload Evenly<a name="bp-partition-key-uniform-load"></a>

The partition key portion of a table's primary key determines the logical partitions in which a table's data is stored\. This in turn affects the underlying physical partitions\. A partition key design that doesn't distribute I/O requests evenly can create "hot" partitions that result in throttling and use your provisioned I/O capacity inefficiently\.

The optimal usage of a table's provisioned throughput depends not only on the workload patterns of individual items, but also on the partition key design\. This doesn't mean that you must access all partition key values to achieve an efficient throughput level, or even that the percentage of accessed partition key values must be high\. It does mean that the more distinct partition key values that your workload accesses, the more those requests will be spread across the partitioned space\. In general, you will use your provisioned throughput more efficiently as the ratio of partition key values accessed to the total number of partition key values increases\.

The following is a comparison of the provisioned throughput efficiency of some common partition key schemas\.


****  

| Partition key value | Uniformity | 
| --- | --- | 
| User ID, where the application has many users\. | Good | 
| Status code, where there are only a few possible status codes\. | Bad | 
| Item creation date, rounded to the nearest time period \(for example, day, hour, or minute\)\. | Bad | 
| Device ID, where each device accesses data at relatively similar intervals\. | Good | 
| Device ID, where even if there are many devices being tracked, one is by far more popular than all the others\. | Bad  | 

If a single table has only a small number of partition key values, consider distributing your write operations across more distinct partition key values\. In other words, structure the primary key elements to avoid one "hot" \(heavily requested\) partition key value that slows overall performance\. 

For example, consider a table with a composite primary key\. The partition key represents the item's creation date, rounded to the nearest day\. The sort key is an item identifier\. On a given day, say `2014-07-09`, **all** of the new items are written to that single partition key value \(and corresponding physical partition\)\. 

If the table fits entirely into a single partition \(considering growth of your data over time\), and if your application's read and write throughput requirements don't exceed the read and write capabilities of a single partition, your application won't encounter any unexpected throttling as a result of partitioning\. 