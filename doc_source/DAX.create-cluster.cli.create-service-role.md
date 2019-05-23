# Step 1: Create an IAM service role for DAX to access DynamoDB<a name="DAX.create-cluster.cli.create-service-role"></a>

Before you can create a DAX cluster, you will need to create a service role for it\. A *service role* is an IAM role that authorizes an AWS service to act on your behalf\. The service role will allow DAX to access your DynamoDB tables, as if you were accessing those tables yourself\. 

In this step, you will create an IAM policy, and then attach that policy to an IAM role\. This will enable you to assign the role to a DAX cluster so that it can perform DynamoDB operations on your behalf\.

1. Create a file named `service-trust-relationship.json` with the following contents:

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

1. Create the service role:

   ```
   aws iam create-role \
       --role-name DAXServiceRoleForDynamoDBAccess \
       --assume-role-policy-document file://service-trust-relationship.json
   ```

1. Create a file named `service-role-policy.json` with the following contents:

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Action": [
                   "dynamodb:*"
               ],
               "Effect": "Allow",
               "Resource": [
                   "arn:aws:dynamodb:us-west-2:accountID:*"
               ]
           }
       ]
   }
   ```

   Replace *accountID* with your AWS account ID\. To find your AWS account ID, go to the upper right\-hand portion of the AWS Management Console and choose your login ID\. Your AWS account ID appears in the drop\-down menu\. \(In the ARN, *accountID* must be a twelve\-digit number\. Do not use hyphens or any other punctuation\.\)

1. Create an IAM policy for the service role:

   ```
   aws iam create-policy \
       --policy-name DAXServicePolicyForDynamoDBAccess \
       --policy-document file://service-role-policy.json
   ```

   In the output, take note of the ARN for the policy you created\. For example:

   `arn:aws:iam::123456789012:policy/DAXServicePolicyForDynamoDBAccess`

1. Attach the policy to the service role:

   ```
   aws iam attach-role-policy \
       --role-name DAXServiceRoleForDynamoDBAccess \
       --policy-arn arn
   ```

   Replace *arn* with the actual role ARN from the previous step\.