# Step 1: Create a Subnet Group<a name="DAX.create-cluster.console.create-subnet-group"></a>

**Note**  
If you have already created a subnet group for your default VPC, you can skip this step\.

DAX is designed to run within an Amazon Virtual Private Cloud environment \(Amazon VPC\)\. If you created your AWS account after 2013\-12\-04, then you already have a default VPC in each AWS region\. For more information, see [Your Default VPC and Subnets](https://docs.aws.amazon.com/vpc/latest/userguide/default-vpc.html) in the Amazon VPC User Guide\.

As part of the creation process for a DAX cluster, you must specify a subnet group\. A *subnet group* is a collection of one or more subnets within your VPC\. When you create your DAX cluster, the nodes will be deployed to the subnets within the subnet group\.

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. In the navigation pane, choose **DAX**\.

1. Choose **Create subnet group**\.

1. In the **Create subnet group** window, do the following:

   1. **Name**—type a short name for the subnet group\.

   1. **Description**—type a description for the subnet group\.

   1. **VPC ID**—choose the identifier for your Amazon VPC environment\.

   1. **Subnets**—choose one or more subnets from the list\.
**Note**  
The subnets are distributed among multiple Availability Zones\. If you plan to create a multi\-node DAX cluster \(a primary node and one or more read replicas\), we recommend that you choose multiple subnet IDs, so that DAX can deploy the cluster nodes into multiple Availability Zones\. If an Availability Zone becomes unavailable, DAX will automatically fail over to a surviving Availability Zone, and your DAX cluster will continue to function without interruption\.

   When the settings are as you want them, click **Create subnet group**\.