# Step 4: Configure Security Group Inbound Rules<a name="DAX.create-cluster.cli.configure-inbound-rules"></a>

The nodes in your DAX cluster use the default security group for your Amazon VPC\. For the default security group, you will need to authorize inbound traffic on TCP port 8111\. This will allow Amazon EC2 instances in your Amazon VPC to access your DAX cluster\.

**Note**  
If you launched your DAX cluster with a different security group \(other than `default`\), you will need to perform this procedure for that group instead\.

1. To determine the default security group identifier, type the following command:

   ```
   aws ec2 describe-security-groups \
       --filters Name=vpc-id,Values=vpcID,Name=group-name,Values=default \
       --query "SecurityGroups[*].{GroupName:GroupName,GroupId:GroupId}"
   ```

   Replace `vpcID` with your actual VPC ID \(from [Step 2: Create a Subnet Group](DAX.create-cluster.cli.create-subnet-group.md)\)\.

   In the output, take note of the security group identifier\. For example: `sg-01234567`

1. Now do this:

   ```
   aws ec2 authorize-security-group-ingress \
       --group-id sgID --protocol tcp --port 8111
   ```

   Replace `sgID` with your actual security group identifier\.