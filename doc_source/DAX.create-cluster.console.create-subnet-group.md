# Step 1: Create a subnet group using the AWS Management Console<a name="DAX.create-cluster.console.create-subnet-group"></a>

Follow this procedure to create a subnet group for your Amazon DynamoDB Accelerator \(DAX\) cluster using the AWS Management Console\.

**Note**  
If you already created a subnet group for your default VPC, you can skip this step\.

DAX is designed to run within an Amazon Virtual Private Cloud \(Amazon VPC\) environment\. If you created your AWS account after December 4, 2013, you already have a default VPC in each AWS Region\. For more information, see [Default VPC and default subnets](https://docs.aws.amazon.com/vpc/latest/userguide/default-vpc.html) in the *Amazon VPC User Guide*\.

As part of the creation process for a DAX cluster, you must specify a *subnet group*\. A subnet group is a collection of one or more subnets within your VPC\. When you create your DAX cluster, the nodes are deployed to the subnets within the subnet group\.

**To create a subnet group**

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. In the navigation pane, under **DAX**, choose **Subnet groups**\.

1. Choose **Create subnet group**\.

1. In the **Create subnet group** window, do the following:

   1. **Name**—Enter a short name for the subnet group\.

   1. **Description**—Enter a description for the subnet group\.

   1. **VPC ID**—Choose the identifier for your Amazon VPC environment\.

   1. **Subnets**—Choose one or more subnets from the list\.
**Note**  
The subnets are distributed among multiple Availability Zones\. If you plan to create a multi\-node DAX cluster \(a primary node and one or more read replicas\), we recommend that you choose multiple subnet IDs\. Then DAX can deploy the cluster nodes into multiple Availability Zones\. If an Availability Zone becomes unavailable, DAX automatically fails over to a surviving Availability Zone\. Your DAX cluster will continue to function without interruption\.

   When the settings are as you want them, choose **Create subnet group**\.

To create the cluster, see [Step 2: Create a DAX cluster using the AWS Management Console](DAX.create-cluster.console.create-cluster.md)\.