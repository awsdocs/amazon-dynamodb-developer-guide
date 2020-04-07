# Creating a DAX Cluster Using the AWS CLI<a name="DAX.create-cluster.cli"></a>

This section describes how to create an Amazon DynamoDB Accelerator \(DAX\) cluster using the AWS Command Line Interface \(AWS CLI\)\. If you haven't already done so, you must install and configure the AWS CLI\. To do this, see the following instructions in the *AWS Command Line Interface User Guide*:
+ [Installing the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/installing.html)
+ [Configuring the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html)

**Important**  
 To manage DAX clusters using the AWS CLI, install or upgrade to version 1\.11\.110 or higher\. 

All of the AWS CLI examples use the `us-west-2` Region and fictitious account IDs\.

**Topics**
+ [Step 1: Create a Service Role](DAX.create-cluster.cli.create-service-role.md)
+ [Step 2: Create a Subnet Group](DAX.create-cluster.cli.create-subnet-group.md)
+ [Step 3: Create a DAX Cluster](DAX.create-cluster.cli.create-cluster.md)
+ [Step 4: Configure Security Group Inbound Rules](DAX.create-cluster.cli.configure-inbound-rules.md)