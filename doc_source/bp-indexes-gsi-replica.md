# Using Global Secondary Indexes to create an eventually consistent replica<a name="bp-indexes-gsi-replica"></a>

You can use a global secondary index to create an eventually consistent replica of a table\. Creating a replica can allow you to do the following:
+ **Set different provisioned read capacity for different readers\.** For example, suppose that you have two applications: One application handles high\-priority queries and needs the highest levels of read performance, whereas the other handles low\-priority queries that can tolerate throttling of read activity\.

  If both of these applications read from the same table, a heavy read load from the low\-priority application could consume all the available read capacity for the table\. This would throttle the high\-priority application's read activity\.

  Instead, you can create a replica through a global secondary index whose read capacity you can set separate from that of the table itself\. You can then have your low\-priority app query the replica instead of the table\.
+ **Eliminate reads from a table entirely\.** For example, you might have an application that captures a high volume of clickstream activity from a website, and you don't want to risk having reads interfere with that\. You can isolate this table and prevent reads by other applications \(see [Using IAM policy conditions for fine\-grained access control](specifying-conditions.md)\), while letting other applications read a replica created using a global secondary index\.

To create a replica, set up a global secondary index that has the same key schema as the parent table, with some or all of the non\-key attributes projected into it\. In applications, you can direct some or all read activity to this global secondary index rather than to the parent table\. You can then adjust the provisioned read capacity of the global secondary index to handle those reads without changing the parent table's provisioned read capacity\.

There is always a short propagation delay between a write to the parent table and the time when the written data appears in the index\. In other words, your applications should take into account that the global secondary index replica is only *eventually consistent* with the parent table\.

You can create multiple global secondary index replicas to support different read patterns\. When you create the replicas, project only the attributes that each read pattern actually requires\. An application can then consume less provisioned read capacity to obtain only the data it needs rather than having to read the item from the parent table\. This optimization can result in significant cost savings over time\.