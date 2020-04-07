# Using Write Sharding to Distribute Workloads Evenly<a name="bp-partition-key-sharding"></a>

One way to better distribute writes across a partition key space in Amazon DynamoDB is to expand the space\. You can do this in several different ways\. You can add a random number to the partition key values to distribute the items among partitions\. Or you can use a number that is calculated based on something that you're querying on\.

## Sharding Using Random Suffixes<a name="bp-partition-key-sharding-random"></a>

One strategy for distributing loads more evenly across a partition key space is to add a random number to the end of the partition key values\. Then you randomize the writes across the larger space\.

For example, for a partition key that represents today's date, you might choose a random number between `1` and `200` and concatenate it as a suffix to the date\. This yields partition key values like `2014-07-09.1`, `2014-07-09.2`, and so on, through `2014-07-09.200`\. Because you are randomizing the partition key, the writes to the table on each day are spread evenly across multiple partitions\. This results in better parallelism and higher overall throughput\.

However, to read all the items for a given day, you would have to query the items for all the suffixes and then merge the results\. For example, you would first issue a `Query` request for the partition key value `2014-07-09.1`\. Then issue another `Query` for `2014-07-09.2`, and so on, through `2014-07-09.200`\. Finally, your application would have to merge the results from all those `Query` requests\.

## Sharding Using Calculated Suffixes<a name="bp-partition-key-sharding-calculated"></a>

A randomizing strategy can greatly improve write throughput\. But it's difficult to read a specific item because you don't know which suffix value was used when writing the item\. To make it easier to read individual items, you can use a different strategy\. Instead of using a random number to distribute the items among partitions, use a number that you can calculate based upon something that you want to query on\.

Consider the previous example, in which a table uses today's date in the partition key\. Now suppose that each item has an accessible `OrderId` attribute, and that you most often need to find items by order ID in addition to date\. Before your application writes the item to the table, it could calculate a hash suffix based on the order ID and append it to the partition key date\. The calculation might generate a number between 1 and 200 that is fairly evenly distributed, similar to what the random strategy produces\.

A simple calculation would likely suffice, such as the product of the UTF\-8 code point values for the characters in the order ID, modulo 200, \+ 1\. The partition key value would then be the date concatenated with the calculation result\.

With this strategy, the writes are spread evenly across the partition key values, and thus across the physical partitions\. You can easily perform a `GetItem` operation for a particular item and date because you can calculate the partition key value for a specific `OrderId` value\.

To read all the items for a given day, you still must `Query` each of the `2014-07-09.N` keys \(where `N` is 1–200\), and your application then has to merge all the results\. The benefit is that you avoid having a single "hot" partition key value taking all of the workload\.

**Note**  
For a more efficient strategy specifically designed to handle high\-volume time series data, see [Time Series Data](bp-time-series.md)\.