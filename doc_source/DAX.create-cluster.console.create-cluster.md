# Step 2: Create a DAX Cluster Using the AWS Management Console<a name="DAX.create-cluster.console.create-cluster"></a>

Follow this procedure to create an Amazon DynamoDB Accelerator \(DAX\) cluster in your default Amazon VPC\.

**To create a DAX cluster**

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. In the navigation pane, under **DAX**, choose **Clusters**\.

1. Choose **Create cluster**\.

1. In the **Create cluster** window, do the following:

   1. **Cluster name**—Enter a short name for your DAX cluster\.
**Note**  
Since `sudo` and `grep` are reserved keywords, you cannot create a DAX cluster with these words in the cluster name\. For example, `sudo` and `sudocluster` are invalid cluster names\.

   1. **Cluster description**—Enter a description for the cluster\.

   1. **Node types**—Choose the node type for all of the nodes in the cluster\.

   1. **Cluster size**—Choose the number of nodes in the cluster\. A cluster consists of one primary node and up to nine read replicas\.
**Note**  
If you want to create a single\-node cluster, choose **1**\. Your cluster will consist of one primary node\.  
If you want to create a multi\-node cluster, choose a number between **3** \(one primary and two read replicas\) and **10** \(one primary and nine read replicas\)\.  
For production usage, we strongly recommend using DAX with at least three nodes, where each node is placed in a different Availability Zone\. Three nodes are required for a DAX cluster to be fault\-tolerant\.  
A DAX cluster can be deployed with one or two nodes for development or test workloads\. One\- and two\-node clusters are not fault\-tolerant, and we don't recommend using fewer than three nodes for production use\. If a one\- or two\-node cluster encounters software or hardware errors, the cluster can become unavailable or lose cached data\.

   1. Choose **Next**\.

   1. **Subnet group**—Select **Choose existing** and choose the subnet group that you created in [Step 1: Create a Subnet Group Using the AWS Management Console](DAX.create-cluster.console.create-subnet-group.md)\.

   1. **Access control**—Choose the **default** security group\.

   1. **Availability Zones \(AZ\)**—Choose **Automatic**\.

   1. Choose next\.

   1. **IAM service role for DynamoDB access**—Choose **Create new**, and enter the following information:
      + **IAM role name**—Enter a name for an IAM role, for example, `DAXServiceRole`\. The console creates a new IAM role, and your DAX cluster assumes this role at runtime\.
      + Select the box next to **Create policy**\.
      + **IAM role policy**—Choose **Read/Write**\. This allows the DAX cluster to perform read and write operations in DynamoDB\.
      + **New IAM policy name**—Enter a name for an IAM policy, for example, `DAXServicePolicy`\. The console creates a new IAM policy and attaches the policy to the IAM role\.
      + **Target DynamoDB table**—Choose **All tables**\.

   1. **Encryption**—Choose **Enable encryption at rest** and **Enable encryption in transit** For more information, see [DAX Encryption at Rest](DAXEncryptionAtRest.md) and [DAX Encryption in Transit](DAXEncryptionInTransit.md)\.

   A separate service role for DAX to access Amazon EC2 is also required\. DAX automatically creates this service role for you\. For more information, see [Using Service\-Linked Roles for DAX](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/using-service-linked-roles.html)\.

1. When the settings are as you want them, choose **Launch cluster**\.

On the **Clusters** screen, your DAX cluster will be listed with a status of **Creating**\.

**Note**  
Creating the cluster takes several minutes\. When the cluster is ready, its status changes to **Available**\.   
 In the meantime, proceed to [Step 3: Configure Security Group Inbound Rules Using the AWS Management Console](DAX.create-cluster.console.configure-inbound-rules.md) and follow the instructions there\.