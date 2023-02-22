# DAX cluster sizing guide<a name="DAX.sizing-guide"></a>

This guide provides advice for choosing an appropriate Amazon DynamoDB Accelerator \(DAX\) cluster size and node type for your application\. These instructions guide you through the steps of estimating your application’s DAX traffic, selecting a cluster configuration, and testing it\.

If you have an existing DAX cluster and want to evaluate whether it has the appropriate number and size of nodes, please refer to [Scaling a DAX cluster](DAX.cluster-management.md#DAX.cluster-management.scaling)\.

**Topics**
+ [Overview](#DAX.sizing-guide.overview)
+ [Estimating traffic](#DAX.sizing-guide.estimating-traffic)
+ [Load testing](#DAX.sizing-guide.load-testing)

## Overview<a name="DAX.sizing-guide.overview"></a>

It's important to scale your DAX cluster appropriately for your workload, whether you're creating a new cluster or maintaining an existing cluster\. As time goes on and your application's workload changes, you should periodically revisit your scaling decisions to make sure that they are still appropriate\.

The process typically follows these steps:

1. **Estimating traffic\.** In this step, you make predictions about the volume of traffic that your application will send to DAX, the nature of the traffic \(read vs\. write operations\), and the expected cache hit rate\.

1. **Load testing\.** In this step, you create a cluster and send traffic to it mirroring your estimates from the previous step\. Repeat this step until you find a suitable cluster configuration\.

1. **Production monitoring\.** While your application is using DAX in production, you should [monitor the cluster](DAX.Monitoring.md) to continuously validate that it is still scaled correctly as your workload changes over time\.

## Estimating traffic<a name="DAX.sizing-guide.estimating-traffic"></a>

There are three main factors that characterize a typical DAX workload:
+ Cache hit rate
+ [Read capacity units](ProvisionedThroughput.md#ProvisionedThroughput.CapacityUnits.Read) \(RCUs\) per second
+ [Write capacity units](ProvisionedThroughput.md#ProvisionedThroughput.CapacityUnits.Write) \(WCUs\) per second

### Estimating cache hit rate<a name="DAX.sizing-guide.estimating-traffic.hit-rate"></a>

If you already have a DAX cluster, you can use the `ItemCacheHits` and `ItemCacheMisses` [Amazon CloudWatch metrics](dax-metrics-dimensions-dax.md) to determine the cache hit rate\. The cache hit rate is equal to `ItemCacheHits` / \(`ItemCacheHits` \+ `ItemCacheMisses`\)\. If your workload includes `Query` or `Scan` operations, you should also look at the `QueryCacheHits`, `QueryCacheMisses`, `ScanCacheHits`, and `ScanCacheMisses` metrics\. Cache hit rates vary from one application to another and are heavily influenced by the cluster's Time to Live \(TTL\) setting\. Typical hit rates for applications using DAX are 85–95 percent\.

### Estimating read and write capacity units<a name="DAX.sizing-guide.estimating-traffic.rcu-wcu"></a>

If you already have DynamoDB tables for your application, look at the `ConsumedReadCapacityUnits` and `ConsumedWriteCapacityUnits` [CloudWatch metrics](dax-metrics-dimensions-dax.md)\. Use the `Sum` statistic and divide by the number of seconds in the period\.

If you also already have a DAX cluster, remember that the DynamoDB `ConsumedReadCapacityUnits` metric only accounts for cache misses\. So, to get an idea of the read capacity units per second handled by your DAX cluster, divide the number by your cache miss rate \(that is, 1 \- cache hit rate\)\.

If you don't already have a DynamoDB table, see the documentation about [read capacity units](ProvisionedThroughput.md#ProvisionedThroughput.CapacityUnits.Read) and [write capacity units](ProvisionedThroughput.md#ProvisionedThroughput.CapacityUnits.Write) to estimate your traffic based on your application's estimated request rate, items accessed per request, and item size\.

When making traffic estimates, plan for future growth and for expected and unexpected peaks to ensure that your cluster has enough headroom for traffic increases\.

## Load testing<a name="DAX.sizing-guide.load-testing"></a>

The next step after estimating traffic is to test the cluster configuration under load\.

1. For your initial load test, we recommend that you start with the `dax.r4.large` node type, the lowest\-cost fixed performance, memory\-optimized node type\.

1. A fault\-tolerant cluster requires at least three nodes, spread across three Availability Zones\. In this case, if an Availability Zone becomes unavailable, the effective number of Availability Zones is reduced by one\-third\. For your initial load test, we recommend that you start with a two\-node cluster, which simulates the failure of one Availability Zone in a three\-node cluster\.

1. Drive sustained traffic \(as estimated in the previous step\) to your test cluster for the duration of the load test\.

1. Monitor the performance of the cluster during the load test\.

Ideally, the traffic profile that you drive during the load test should be as similar as possible to your application's real traffic\. This includes the distribution of operations \(for example, 70 percent `GetItem`, 25 percent `Query`, and 5 percent `PutItem`\), the request rate for each operation, the number of items accessed per request, and the distribution of item sizes\. To achieve a cache hit rate similar to your application's expected cache hit rate, pay close attention to the distribution of keys in your test traffic\.

**Note**  
Be careful when load testing T2 node types \(`dax.t2.small` and `dax.t2.medium`\)\. T2 node types provide [burstable CPU performance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/burstable-performance-instances.html) that varies over time depending on the node's CPU credit balance\. A DAX cluster running on T2 nodes might appear to be operating normally, but if any node is bursting above the [baseline performance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/burstable-credits-baseline-concepts.html) of its instance, the node is spending its accrued CPU credit balance\. When the credit balance runs low, [performance is gradually lowered](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/burstable-performance-instances-standard-mode.html) to the baseline performance level\.

[Monitor your DAX cluster](DAX.Monitoring.md) during the load test to determine whether the node type that you're using for the load test is the right node type for you\. In addition, during a load test, you should monitor your request rate and cache hit rate to ensure that your test infrastructure is actually driving the amount of traffic you intend\.

If load testing indicates that the selected cluster configuration can't sustain your application's workload, we recommend [switching to a larger node type](DAX.cluster-management.md#DAX.cluster-management.scaling.node-types), especially if you see high CPU utilization on the primary node in the cluster, high eviction rates, or high cache memory utilization\. If hit rates are consistently high, and the ratio of read to write traffic is high, you may want to consider [adding more nodes to your cluster](DAX.cluster-management.md#DAX.cluster-management.scaling.read-scaling)\. Refer to [Scaling a DAX cluster](DAX.cluster-management.md#DAX.cluster-management.scaling) for additional guidance on when to use a larger node type \(vertical scaling\) or add more nodes \(horizontal scaling\)\.

You should repeat your load test after making changes to your cluster configuration\.