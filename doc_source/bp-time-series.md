# Best Practices for Handling Time\-Series Data in DynamoDB<a name="bp-time-series"></a>

General design principles in DynamoDB recommend that you keep the number of tables you use to a minimum\. For most applications, a single table is all you need\. However, for time\-series data, or any data with monotonically increasing or decreasing primary\-key values, you can often best handle it by using a design pattern that doesn't follow that guideline\.

## Design Pattern for Time\-Series Data<a name="bp-time-series-pattern"></a>

Consider a typical time\-series scenario, where you want to track a high volume of events\. Your write access pattern is that all the events being recorded have today's date\. Your read access pattern might be to read today's events most frequently, yesterday's events much less frequently, and then older events very little at all\.

The read access pattern is best handled by building the current date and time into the primary key\. But that is certain to create one or more hot partitions\. The latest one is always the *only* partition that is being written to\. All other partitions, including all the partitions from previous days, divert provisioned write capacity from where you need it most\.

The following design pattern often handles this kind of scenario effectively:
+ Create one table per time period, provisioned with write capacity less than 1,000 write capacity units \(WCUs\) per partition\-key value, and minimum necessary read capacity\.
+ Before the end of each time period, prebuild the table for the next period\. Just as the current period ends, direct event traffic to the new table\. You can assign names to these tables that specify the time periods that they have recorded\.
+ As soon as a table is no longer being written to, reduce its provisioned write capacity to 1 WCU and provision whatever read capacity is appropriate\. Reduce the provisioned read capacity of earlier tables as they age, and archive or delete the ones whose contents will rarely or never be needed\.

The idea is to ensure that provisioned write capacity is fully used rather than being diluted by partitions that are not being written to\. With a partition key whose values change monotonically, like a time value, this means trying to prevent any physical partition from being split\. This is because after a split, all subsequent writes will be made **only to one of the two partitions that result from the split**\.

## Designing for High\-Volume Streams of Events<a name="bp-time-series-high-volume"></a>

There are two key numbers to keep in mind for this design\. First, a physical partition can support only 1,000 WCUs \(writes per second, each of up to 1 KB in size\)\. Second, it can hold only 10 GB of total data\. If either of these limits is exceeded, the partition will be split\.

Given that, here is a way to go about designing for a high\-volume stream of incoming events:

1. Determine the size of each event item in DynamoDB\. This should be as small as is practical, and if possible, much smaller than 1 KB, which is the maximum that can be accommodated by a single WCU\.

1. Estimate how many such event items fit into one physical partition \(10 GB\), including a little room for administrative overhead\.

1. Estimate the number of incoming events to be written every second\. Use that figure to calculate the number of seconds that a physical partition can accommodate, on average, before exceeding the 10 GB limit\. Choose a convenient time period slightly smaller than that duration—such as an hour, a 6\- or 12\-hour period, a day, or whatever—to serve as the period for each table\.

   What do you do if your incoming event stream has a cadence higher than 1,000 events per second? 

   Your application can handle this by write\-sharding the partition key you're using \(see [Using Write Sharding to Distribute Workloads Evenly](bp-partition-key-sharding.md)\)\. For example, if you anticipate an average event frequency around 5,000 events per second with a maximum of 6,000 per second, your application can append to the partition key a sharding suffix that rotates from `_1` through `_6` and back again\. This creates six physical partitions across which incoming events are evenly distributed as they arrive, and you can provision your table with 6,000 WCUs\.

1. Program your application to prebuild a new table for each time period\. Provision it with enough throughput capacity to handle the incoming event workload that you anticipate\. When the new table is ready, direct incoming event data to it just as its time period begins, instead of to the previous period's table\.

1. Now that the previous period's table is no longer recording new events, you can dial back its provisioned write capacity to `1` WCU\. You can change its read capacity to whatever is needed to support the anticipated read workload\.

1. After the time period of each table is far enough in the past that it will be needed infrequently, you can archive it to another storage option such as Amazon Simple Storage Service \(Amazon S3\) or Amazon Glacier\. Or, if data in an older table is no longer needed at all, you can delete the whole table\. Doing this is significantly more efficient than moving or removing items one by one\.

## Time\-Series Table Examples<a name="bp-time-series-examples"></a>

The following is an example of time\-series tables designed to handle around 600 events per second, each taking up about 180 bytes in DynamoDB\.

At this rate, 10 GB would fill up in around 26 or 27 hours, so this design puts a new table into service every day at midnight:

![\[Table schema for high-volume time-series data.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/TimeSeries.png)