# Evaluate your table capacity mode<a name="CostOptimization_TableCapacityMode"></a>

This section provides an overview of how to select the appropriate capacity mode for your DynamoDB table\. Each mode is tuned to meet the needs of a different workload in terms of responsiveness to change in throughput, as well as how that usage is billed\. You must balance these factors when making our decision\.

**Topics**
+ [What table capacity modes are available](#CostOptimization_TableCapacityMode_Overview)
+ [When to select on\-demand capacity mode](#CostOptimization_TableCapacityMode_OnDemand)
+ [When to select provisioned capacity mode](#CostOptimization_TableCapacityMode_Provisioned)
+ [Additional factors to consider when choosing a table capacity mode](#CostOptimization_TableCapacityMode_AdditionalFactors)

## What table capacity modes are available<a name="CostOptimization_TableCapacityMode_Overview"></a>

When you create a DynamoDB table, you must select either on\-demand or provisioned capacity mode\. You can change this setting twice in a 24\-hour period, so don’t worry if this needs to be changed in the future\.

![\[Image showing the two different capacity mode options when creating a DynamoDB table: on-demand and provisioned.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/CostOptimization/TableCapacityModeOptions.png)

**On\-demand capacity mode**  
The on\-demand capacity mode is designed to eliminate the need to plan or provision the capacity of your DynamoDB table\. In this mode, your table will instantly accommodate requests to your table without the need to scale any resources up or down \(up to twice the previous peak throughput of the table\)\. 

On\-demand tables are billed by counting the number of actual requests against the table, so you will only pay for what you use rather than what has been provisioned\.

**Provisioned capacity mode**  
The provisioned capacity mode is a more traditional model where you can define how much capacity the table has available for requests either directly or with the assistance of auto\-scaling\. Because a specific capacity is provisioned for the table at any given time, billing is based off of the capacity provisioned rather than the number of requests\. Going over the allocated capacity can also cause the table to reject requests and reduce the experience of your applications users\.

Provisioned capacity mode requires a balance between not overprovisioning or under provisioning the table to keep both throttling low and costs tuned\.

## When to select on\-demand capacity mode<a name="CostOptimization_TableCapacityMode_OnDemand"></a>

When optimizing for cost, on\-demand mode is your best choice when you have a workload similar to the following graph\.

The following factors contribute to this type of workload:
+ Unpredictable request timing \(resulting in traffic spikes\)
+ Variable volume of requests \(resulting from batch workloads\)
+ Drops to zero or below 18% of the peak for a given hour \(resulting from dev or test environments\)

![\[Image showing a spiky workload with random peaks in traffic.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/CostOptimization/TableCapacityModeOnDemand.png)

For workloads with the above factors, using Auto\-Scaling to maintain enough capacity on the table to respond to spikes in traffic will likely lead to the table being overprovisioned and costing more than necessary or the table being under provisioned and requests being unnecessarily throttled\.

Because on\-demand tables are billed by request, there is nothing further we need to do at the table level to optimize for cost\. You should regularly evaluate your on\-demand tables to verify the workload still has the above factors\. If the workload has stabilized, consider changing to provisioned mode to further optimize cost\.

## When to select provisioned capacity mode<a name="CostOptimization_TableCapacityMode_Provisioned"></a>

An ideal workload for provisioned capacity mode is one with a more predictable usage pattern like the graph below\.

The following factors contribute to this type of workload:
+ Predicable/cyclical traffic for a given hour or day
+ Limited short term bursts of traffic

![\[Image showing a fairly predictable workload with limited peaks in traffic.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/CostOptimization/TableCapacityModeProvisioned.png)

Since the traffic volumes within a given hour or day are more stable, we can set the provisioned capacity of the table relatively close to the actual consumed capacity of the table\. Cost optimizing a provisioned capacity table is ultimately an exercise in getting the provisioned capacity \(blue line\) as close to the consumed capacity \(orange line\) as possible without increasing `ThrottledRequests` on the table\. The space between the two lines is both wasted capacity as well as insurance against a bad user experience due to throttling\.

DynamoDB provides auto scaling for provisioned capacity tables which will automatically balance this on your behalf\. This lets you track your consumed capacity throughout the day and set the capacity of the table based on a handful of variables\.

![\[Image showing a fairly predictable workload with limited peaks in traffic.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/CostOptimization/TableCapacityModeAutoScaling.png)

**Minimum capacity units**  
You can set the minimum capacity of a table to limit throttling, but it will not reduce the cost of the table\. If your table has periods of low usage follow by a sudden burst of high usage, setting the minimum can prevent auto scaling from setting the table capacity too low\.

**Maximum capacity units**  
You can set the maximum capacity of a table to limit a table scaling higher than intended\. Consider applying a maximum for Dev or Test tables where large\-scale load testing is not desired\. You can set a maximum for any table, but be sure to regularly evaluate this setting against the table baseline when using it in Production to prevent accidental throttling\.

**Target utilization**  
Setting the target utilization of the table is the primary means of cost optimization for a provisioned capacity table\. Setting a lower percent value here will increase how much the table is overprovisioned, increasing cost, but reducing the risk of throttling\. Setting a higher percent value will decrease how much the table is overprovisioned, but increase the risk of throttling\.

## Additional factors to consider when choosing a table capacity mode<a name="CostOptimization_TableCapacityMode_AdditionalFactors"></a>

When deciding between the two modes, there are some additional factors worth considering\.

**Reserved capacity**  
For provisioned capacity tables, DynamoDB offers the ability to purchase reserved capacity for your read and write capacity \(replicated write capacity units \(rWCU\) and Standard\-IA tables are currently not eligible\)\. If you chose to purchase reservations for this capacity, you can reduce the cost of the table by a significant percent\.

 When deciding between the two table modes, consider how much this additional discount will affect the cost of the table\. In many cases, even a relatively unpredictable workload can be cheaper to run on an overprovisioned provisioned capacity table with reserved capacity\. 

**Improving predictability of your workload**  
In some situations, a workload may seemingly have both a predictable and unpredictable pattern\. While this can be easily supported with an on\-demand table, costs will likely be better if the unpredictable patterns in the workload can be improved\.

One of the most common causes of these patterns is batch imports\. This type of traffic can often exceed the baseline capacity of the table to such a degree that throttling would occur if it were to run\. To keep a workload like this running on a provisioned capacity table, consider the following options:
+ If the batch occurs at scheduled times, you can schedule an increase to your auto\- scaling capacity before it runs
+ If the batch occurs randomly, consider trying to extend the time it runs rather than executing as fast as possible
+ Add a ramp up period to the import where the velocity of the import starts small but is slowly increased over a few minutes until auto\-scaling has had the opportunity to start adjusting table capacity