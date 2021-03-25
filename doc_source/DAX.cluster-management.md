# Managing DAX Clusters<a name="DAX.cluster-management"></a>

This section addresses some of the common management tasks for Amazon DynamoDB Accelerator \(DAX\) clusters\.

**Topics**
+ [IAM Permissions for Managing a DAX Cluster](#DAX.cluster-management.iam-permissions)
+ [Scaling a DAX Cluster](#DAX.cluster-management.scaling)
+ [Customizing DAX Cluster Settings](#DAX.cluster-management.custom-settings)
+ [Configuring TTL Settings](#DAX.cluster-management.custom-settings.ttl)
+ [Tagging Support for DAX](#DAX.management.tagging)
+ [AWS CloudTrail Integration](#DAX.management.cloudtrail)
+ [Deleting a DAX Cluster](#DAX.cluster-management.deleting)

## IAM Permissions for Managing a DAX Cluster<a name="DAX.cluster-management.iam-permissions"></a>

When you administer a DAX cluster using the AWS Management Console or the AWS Command Line Interface \(AWS CLI\), we strongly recommend that you narrow the scope of actions that users can perform\. By doing so, you help mitigate risk while following the principle of least privilege\.

The following discussion focuses on access control for the DAX management APIs\. For more information, see [Amazon DynamoDB Accelerator](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Operations_Amazon_DynamoDB_Accelerator_(DAX).html) in the *Amazon DynamoDB API Reference*\.

**Note**  
For more detailed information about managing AWS Identity and Access Management \(IAM\) permissions, see the following:  
IAM and creating DAX clusters: [Creating a DAX Cluster](DAX.create-cluster.md)\.
IAM and DAX data plane operations: [DAX Access Control](DAX.access-control.md)\.

For the DAX management APIs, you can't scope API actions to a specific resource\. The `Resource` element must be set to `"*"`\. This is different from DAX data plane API operations, such as `GetItem`, `Query`, and `Scan`\. Data plane operations are exposed through the DAX client, and those operations *can* be scoped to specific resources\.

To illustrate, consider the following IAM policy document\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "dax:*"
            ],
            "Effect": "Allow",
            "Resource": [
                "arn:aws:dax:us-west-2:123456789012:cache/DAXCluster01"
            ]
        }
    ]
}
```

Suppose that the intent of this policy is to allow DAX management API calls for the cluster `DAXCluster01`— and only that cluster\.

Now suppose that a user issues the following AWS CLI command\.

```
aws dax describe-clusters
```

This command fails with a Not Authorized exception because the underlying `DescribeClusters` API call can't be scoped to a specific cluster\. Even though the policy is syntactically valid, the command fails because the `Resource` element must be set to `"*"`\. However, if the user runs a program that sends DAX data plane calls \(such as `GetItem` or `Query`\) to `DAXCluster01`, those calls *do* succeed\. This is because DAX data plane APIs can be scoped to specific resources \(in this case, `DAXCluster01`\)\.

If you want to write a single comprehensive IAM policy to encompass both DAX management APIs and DAX data plane APIs, we suggest that you include two distinct statements in the policy document\. One of these statements should address the DAX data plane APIs, while the other statement addresses the DAX management APIs\.

The following example policy shows this approach\. Note how the `DAXDataAPIs` statement is scoped to the `DAXCluster01` resource, but the resource for `DAXManagementAPIs` must be `"*"`\. The actions shown in each statement are for illustration only\. You can customize them as needed for your application\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "DAXDataAPIs",
            "Action": [
                "dax:GetItem",
                "dax:BatchGetItem",
                "dax:Query",
                "dax:Scan",
                "dax:PutItem",
                "dax:UpdateItem",
                "dax:DeleteItem",
                "dax:BatchWriteItem"
            ],
            "Effect": "Allow",
            "Resource": [
                "arn:aws:dax:us-west-2:123456789012:cache/DAXCluster01"
            ]},
            {
            "Sid": "DAXManagementAPIs",
            "Action": [
                "dax:CreateParameterGroup",
                "dax:CreateSubnetGroup",
                "dax:DecreaseReplicationFactor",
                "dax:DeleteCluster",
                "dax:DeleteParameterGroup",
                "dax:DeleteSubnetGroup",
                "dax:DescribeClusters",
                "dax:DescribeDefaultParameters",
                "dax:DescribeEvents",
                "dax:DescribeParameterGroups",
                "dax:DescribeParameters",
                "dax:DescribeSubnetGroups",
                "dax:IncreaseReplicationFactor",
                "dax:ListTags",
                "dax:RebootNode",
                "dax:TagResource",
                "dax:UntagResource",
                "dax:UpdateCluster",
                "dax:UpdateParameterGroup",
                "dax:UpdateSubnetGroup"
                ],
            "Effect": "Allow",
            "Resource": [
                "*"
            ]
        }
    ]
}
```

## Scaling a DAX Cluster<a name="DAX.cluster-management.scaling"></a>

There are two options available for scaling a DAX cluster\. The first option is *horizontal scaling*, where you add read replicas to the cluster\. The second option is *vertical scaling*, where you select different node types\. For advice on how to approach choosing an appropriate cluster size and node type for your application, see [DAX Cluster Sizing Guide](DAX.sizing-guide.md)\.

### Horizontal Scaling<a name="DAX.cluster-management.scaling.read-scaling"></a>

With horizontal scaling, you can improve throughput for read operations by adding more read replicas to the cluster\. A single DAX cluster supports up to 10 read replicas, and you can add or remove replicas while the cluster is running\.

The following AWS CLI examples show how to increase or decrease the number of nodes\. The `--new-replication-factor` argument specifies the total number of nodes in the cluster\. One of the nodes is the primary node, and the other nodes are read replicas\.

```
aws dax increase-replication-factor \
    --cluster-name MyNewCluster  \
    --new-replication-factor 5
```

```
aws dax decrease-replication-factor \
    --cluster-name MyNewCluster  \
    --new-replication-factor 3
```

**Note**  
The cluster status changes to `modifying` when you modify the replication factor\. The status changes to `available` when the modification is complete\.

### Vertical Scaling<a name="DAX.cluster-management.scaling.node-types"></a>

If you have a large working set of data, your application might benefit from using larger node types\. Larger nodes can enable the cluster to store more data in memory, reducing cache misses and improving overall application performance of the application\. \(All of the nodes in a DAX cluster must be of the same type\.\)

If your DAX cluster has a high rate of write operations or cache misses, your application might also benefit from using larger node types\. Write operations and cache misses consume resources on the cluster's primary node\. Therefore, using larger node types might increase the performance of the primary node and thereby allow a higher throughput for these types of operations\.

You can't modify the node types on a running DAX cluster\. Instead, you must create a new cluster with the desired node type\. For a list of supported node types, see [Nodes](DAX.concepts.cluster.md#DAX.concepts.nodes)\.

You can create a new DAX cluster using the AWS Management Console, [AWS CloudFormation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-dax-cluster.html), the AWS CLI, or the [AWS SDK](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Operations_Amazon_DynamoDB_Accelerator_(DAX).html)\. \(For the AWS CLI, use the `--node-type` parameter to specify the node type\.\)

## Customizing DAX Cluster Settings<a name="DAX.cluster-management.custom-settings"></a>

When you create a DAX cluster, the following default settings are used:
+ Automatic cache eviction enabled with Time to Live \(TTL\) of 5 minutes
+ No preference for Availability Zones
+ No preference for maintenance windows
+ Notifications disabled

For new clusters, you can customize the settings at creation time\. To do this in the AWS Management Console, clear **Use default settings** to modify the following settings:
+ **Network and Security**—Allows you to run individual DAX cluster nodes in different Availability Zones within the current AWS Region\. If you choose **No Preference**, the nodes are distributed among Availability Zones automatically\.
+ **Parameter Group**—A named set of parameters that are applied to every node in the cluster\. You can use a parameter group to specify cache TTL behavior\. You can change the value of any given parameter within a parameter group \(except default parameter group `default.dax.1.0`\) at any time\.
+ **Maintenance Window**—A weekly time period during which software upgrades and patches are applied to the nodes in the cluster\. You can choose the start day, start time, and duration of the maintenance window\. If you choose **No Preference**, the maintenance window is selected at random from an 8\-hour block of time per Region\. For more information, see [Maintenance Window](DAX.concepts.cluster.md#DAX.concepts.maintenance-window)\.

**Note**  
**Parameter Group** and **Maintenance Window** can also be changed at any time on a running cluster\.

When a maintenance event occurs, DAX can notify you using Amazon Simple Notification Service \(Amazon SNS\)\. To configure notifications, choose an option from the **Topic for SNS notification** selector\. You can create a new Amazon SNS topic, or use an existing topic\.

For more information about setting up and subscribing to an Amazon SNS topic, see [Getting Started with Amazon SNS](https://docs.aws.amazon.com/sns/latest/dg/GettingStarted.html) in the *Amazon Simple Notification Service Developer Guide*\.

## Configuring TTL Settings<a name="DAX.cluster-management.custom-settings.ttl"></a>

DAX maintains two caches for data that it reads from DynamoDB:
+ **Item cache**—For items retrieved using `GetItem` or `BatchGetItem`\.
+ **Query cache**—For result sets retrieved using `Query` or `Scan`\.

For more information, see [Item Cache](DAX.concepts.md#DAX.concepts.item-cache) and [Query Cache](DAX.concepts.md#DAX.concepts.query-cache)\.

The default TTL for each of these caches is 5 minutes\. If you want to use different TTL settings, you can launch a DAX cluster using a custom parameter group\. To do this on the console, choose **DAX \| Parameter groups** in the navigation pane\.

You can also perform these tasks using the AWS CLI\. The following example shows how to launch a new DAX cluster using a custom parameter group\. In this example, the item cache TTL is set to 10 minutes, and the query cache TTL is set to 3 minutes\.

1. Create a new parameter group\.

   ```
   aws dax create-parameter-group \
       --parameter-group-name custom-ttl
   ```

1. Set the item cache TTL to 10 minutes \(600000 milliseconds\)\.

   ```
   aws dax update-parameter-group \
       --parameter-group-name custom-ttl \
       --parameter-name-values "ParameterName=record-ttl-millis,ParameterValue=600000"
   ```

1. Set the query cache TTL to 3 minutes \(180000 milliseconds\)\.

   ```
   aws dax update-parameter-group \
       --parameter-group-name custom-ttl \
       --parameter-name-values "ParameterName=query-ttl-millis,ParameterValue=180000"
   ```

1. Verify that the parameters have been set correctly\.

   ```
   aws dax describe-parameters --parameter-group-name custom-ttl \
       --query "Parameters[*].[ParameterName,Description,ParameterValue]"
   ```

You can now launch a new DAX cluster with this parameter group\.

```
aws dax create-cluster \
    --cluster-name MyNewCluster \
    --node-type dax.r3.large \
    --replication-factor 3 \
    --iam-role-arn arn:aws:iam::123456789012:role/DAXServiceRole \
    --parameter-group custom-ttl
```

**Note**  
You can't modify a parameter group that is being used by a running DAX instance\.

## Tagging Support for DAX<a name="DAX.management.tagging"></a>

Many AWS services, including DynamoDB, support *tagging*—the ability to label resources with user\-defined names\. You can assign tags to DAX clusters, allowing you to quickly identify all of your AWS resources that have the same tag, or to categorize your AWS bills by the tags you assign\.

For more information, see [Adding Tags and Labels to Resources](Tagging.md)\.

### Using the AWS Management Console<a name="DAX.management.tagging.console"></a>

**To manage DAX cluster tags**

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. In the navigation pane, under **DAX**, choose **Clusters**\.

1. Choose the cluster that you want to work with\.

1. Choose the **Tags** tab\. You can add, list, edit, or delete your tags here\.

   When the settings are as you want them, choose **Apply Changes**\.

### Using the AWS CLI<a name="DAX.management.tagging.cli"></a>

When you use the AWS CLI to manage DAX cluster tags, you must first determine the Amazon Resource Name \(ARN\) for the cluster\. The following example shows how to determine the ARN for a cluster named `MyDAXCluster`\.

```
aws dax describe-clusters \
    --cluster-name MyDAXCluster \
    --query "Clusters[*].ClusterArn"
```

In the output, the ARN will look similar to this: `arn:aws:dax:us-west-2:123456789012:cache/MyDAXCluster`

The following example shows how to tag the cluster\.

```
aws dax tag-resource  \
    --resource-name arn:aws:dax:us-west-2:123456789012:cache/MyDAXCluster \
    --tags="Key=ClusterUsage,Value=prod"
```

List all the tags for a cluster\.

```
aws dax list-tags \
    --resource-name arn:aws:dax:us-west-2:123456789012:cache/MyDAXCluster
```

To remove a tag, specify its key\.

```
aws dax untag-resource  \
    --resource-name arn:aws:dax:us-west-2:123456789012:cache/MyDAXCluster \
    --tag-keys ClusterUsage
```

## AWS CloudTrail Integration<a name="DAX.management.cloudtrail"></a>

DAX is integrated with AWS CloudTrail, allowing you to audit DAX cluster activities\. You can use CloudTrail logs to view all the changes that have been made at the cluster level\. You can also see changes to cluster components such as nodes, subnet groups, and parameter groups\. For more information, see [Logging DynamoDB Operations by Using AWS CloudTrail](logging-using-cloudtrail.md)\.

## Deleting a DAX Cluster<a name="DAX.cluster-management.deleting"></a>

If you are no longer using a DAX cluster, you should delete it to avoid being charged for unused resources\.

You can delete a DAX cluster using the console or the AWS CLI\. The following is an example\.

```
aws dax delete-cluster --cluster-name mydaxcluster
```