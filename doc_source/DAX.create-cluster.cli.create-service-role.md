# Step 1: Create an IAM service role for DAX to access DynamoDB using the AWS CLI<a name="DAX.create-cluster.cli.create-service-role"></a>

Before you can create an Amazon DynamoDB Accelerator \(DAX\) cluster, you must create a service role for it\. A *service role* is an AWS Identity and Access Management \(IAM\) role that authorizes an AWS service to act on your behalf\. The service role allows DAX to access your DynamoDB tables as if you were accessing those tables yourself\. 

In this step, you create an IAM policy and then attach that policy to an IAM role\. This enables you to assign the role to a DAX cluster so that it can perform DynamoDB operations on your behalf\.

**To create an IAM service role for DAX**

1. Create a file named `service-trust-relationship.json` with the following contents\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
          {
               "Effect": "Allow",
               "Principal": {
                   "Service": "dax.amazonaws.com"
               },
               "Action": "sts:AssumeRole"
           }
       ]
   }
   ```

1. Create the service role\.

   ```
   aws iam create-role \
       --role-name DAXServiceRoleForDynamoDBAccess \
       --assume-role-policy-document file://service-trust-relationship.json
   ```

1. Create a file named `service-role-policy.json` with the following contents\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Action": [
                   "dynamodb:DescribeTable",
                   "dynamodb:PutItem",
                   "dynamodb:GetItem",
                   "dynamodb:UpdateItem",
                   "dynamodb:DeleteItem",
                   "dynamodb:Query",
                   "dynamodb:Scan",
                   "dynamodb:BatchGetItem",
                   "dynamodb:BatchWriteItem",
                   "dynamodb:ConditionCheckItem"
               ],
               "Effect": "Allow",
               "Resource": [
                   "arn:aws:dynamodb:us-west-2:accountID:*"
               ]
           }
       ]
   }
   ```

   Replace *accountID* with your AWS account ID\. To find your AWS account ID, in the upper\-right corner of the console, choose your login ID\. Your AWS account ID appears in the drop\-down menu\. 

   In the Amazon Resource Name \(ARN\) in the example, *accountID* must be a 12\-digit number\. Don't use hyphens or any other punctuation\.

1. Create an IAM policy for the service role\.

   ```
   aws iam create-policy \
       --policy-name DAXServicePolicyForDynamoDBAccess \
       --policy-document file://service-role-policy.json
   ```

   In the output, note the ARN for the policy that you created, as in the following example\.

   `arn:aws:iam::123456789012:policy/DAXServicePolicyForDynamoDBAccess`

1. Attach the policy to the service role\. Replace *arn* in the following code with the actual role ARN from the previous step\.

   ```
   aws iam attach-role-policy \
       --role-name DAXServiceRoleForDynamoDBAccess \
       --policy-arn arn
   ```

Next, you specify a subnet group for your default VPC\. A *subnet group* is a collection of one or more subnets within your VPC\. See [Step 2: Create a subnet group](DAX.create-cluster.cli.create-subnet-group.md)\.