# Step 3: Create a DAX Cluster<a name="DAX.create-cluster.cli.create-cluster"></a>

Follow this procedure to create a DAX cluster in your default Amazon VPC:

****

1. Get the Amazon Resource Name \(ARN\) for your service role:

   ```
   aws iam get-role \
       --role-name DAXServiceRoleForDynamoDBAccess \
       --query "Role.Arn" --output text
   ```

   In the output, take note of the service role ARN\. For example:

   `arn:aws:iam::123456789012:role/DAXServiceRoleForDynamoDBAccess`

1. You are now ready to create your DAX cluster:

   ```
   aws dax create-cluster \
       --cluster-name mydaxcluster \
       --node-type dax.r4.large \
       --replication-factor 3 \
       --iam-role-arn roleARN \
       --subnet-group my-subnet-group \
       --sse-specification Enabled=true \
       --region us-west-2
   ```

   Replace `roleARN` with the ARN from the previous step\.

   All of the nodes in the cluster will be of type *dax\.r3\.large* \(`--node-type`\)\. There will be three nodes \(`--replication-factor`\)—one primary node and two replicas\.

To view the cluster status, type the following command:

```
aws dax describe-clusters
```

The status is shown in the output\. For example: `"Status": "creating"`

**Note**  
Creating the cluster will take several minutes\. When the cluster is ready, its status changes to `available`\.  
 In the meantime, you can proceed to [Step 4: Configure Security Group Inbound Rules](DAX.create-cluster.cli.configure-inbound-rules.md) and follow the instructions there\.