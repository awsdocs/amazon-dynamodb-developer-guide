# Step 4: Configure Security Group Inbound Rules Using the AWS CLI<a name="DAX.create-cluster.cli.configure-inbound-rules"></a>

The nodes in your Amazon DynamoDB Accelerator \(DAX\) cluster use the default security group for your Amazon VPC\. For the default security group, you must authorize inbound traffic on TCP port 8111 for unencrypted clusters or port 9111 for encrypted clusters\. This allows Amazon EC2 instances in your Amazon VPC to access your DAX cluster\.

**Note**  
If you launched your DAX cluster with a different security group \(other than `default`\), you must perform this procedure for that group instead\.

**To configure security group inbound rules**

1. To determine the default security group identifier, enter the following command\. Replace `vpcID` with your actual VPC ID \(from [Step 2: Create a Subnet Group](DAX.create-cluster.cli.create-subnet-group.md)\)\.

   ```
   aws ec2 describe-security-groups \
       --filters Name=vpc-id,Values=vpcID,Name=group-name,Values=default \
       --query "SecurityGroups[*].{GroupName:GroupName,GroupId:GroupId}"
   ```

   In the output, note the security group identifier—for example, `sg-01234567`\.

1. Then enter the following\. Replace `sgID` with your actual security group identifier\. Use port `8111` for unencrypted clusters and `9111` for encrypted clusters\.

   ```
   aws ec2 authorize-security-group-ingress \
       --group-id sgID --protocol tcp --port 8111
   ```