# Step 2: Create a Subnet Group<a name="DAX.create-cluster.cli.create-subnet-group"></a>

Follow this procedure to create a subnet group for your Amazon DynamoDB Accelerator \(DAX\) cluster using the AWS Command Line Interface \(AWS CLI\)\. 

**Note**  
If you already created a subnet group for your default VPC, you can skip this step\. 

DAX is designed to run within an Amazon Virtual Private Cloud environment \(Amazon VPC\)\. If you created your AWS account after December 4, 2013, you already have a default VPC in each AWS Region\. For more information, see [Default VPC and Default Subnets](https://docs.aws.amazon.com/vpc/latest/userguide/default-vpc.html) in the *Amazon VPC User Guide*\.

**To create a subnet group**

1. To determine the identifier for your default VPC, enter the following command\.

   ```
   aws ec2 describe-vpcs
   ```

   In the output, note the identifier for your default VPC, as in the following example\.

   `vpc-12345678`

1. Determine the subnet IDs associated with your default VPC\. Replace *vpcID* with your actual VPC ID—for example, `vpc-12345678`\.

   ```
   aws ec2 describe-subnets \
       --filters "Name=vpc-id,Values=vpcID" \
       --query "Subnets[*].SubnetId"
   ```

   In the output, note the subnet identifiers—for example, `subnet-11111111`\.

1. Create the subnet group\. Ensure that you specify at least one subnet ID in the `--subnet-ids` parameter\.

   ```
   aws dax create-subnet-group \
       --subnet-group-name my-subnet-group \
       --subnet-ids subnet-11111111 subnet-22222222 subnet-33333333 subnet-44444444
   ```

To create the cluster, see [Step 3: Create a DAX Cluster Using the AWS CLI](DAX.create-cluster.cli.create-cluster.md)\.