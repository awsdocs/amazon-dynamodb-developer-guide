# Step 2: Create a Subnet Group<a name="DAX.create-cluster.cli.create-subnet-group"></a>

**Note**  
If you have already created a subnet group for your default VPC, you can skip this step\. 

DAX is designed to run within an Amazon Virtual Private Cloud environment \(Amazon VPC\)\. If you created your AWS account after 2013\-12\-04, then you already have a default VPC in each AWS region\. For more information, see [Your Default VPC and Subnets](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/default-vpc.html) in the Amazon VPC User Guide\.

As part of the creation process for a DAX cluster, you must specify a subnet group\. A *subnet group* is a collection of one or more subnets within your VPC\. When you create your DAX cluster, the nodes will be deployed to the subnets within the subnet group\.

1. To determine the identifier for your default VPC, type the following command:

   ```
   aws ec2 describe-vpcs
   ```

   In the output, take note of the identifier for your default VPC\. For example: 

   `vpc-12345678`

1. Determine the subnets IDs associated with your default VPC:

   ```
   aws ec2 describe-subnets \
       --filters "Name=vpc-id,Values=vpcID" \
       --query "Subnets[*].SubnetId"
   ```

   Replace *vpcID* with your actual VPC ID\. For example: `vpc-12345678`

   In the output, take note of the subnet identifiers\. For example: `subnet-11111111`

1. Create the subnet group\. Ensure that you specify at least one subnet ID in the `--subnet-ids` parameter:

   ```
   aws dax create-subnet-group \
       --subnet-group-name my-subnet-group \
       --subnet-ids subnet-11111111 subnet-22222222 subnet-33333333 subnet-44444444
   ```