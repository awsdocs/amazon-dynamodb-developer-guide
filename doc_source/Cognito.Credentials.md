# Configuring AWS credentials in your files using Amazon Cognito<a name="Cognito.Credentials"></a>

The recommended way to obtain AWS credentials for your web and mobile applications is to use Amazon Cognito\. Amazon Cognito helps you avoid hardcoding your AWS credentials on your files\. It uses AWS Identity and Access Management \(IAM\) roles to generate temporary credentials for your application's authenticated and unauthenticated users\.

 For example, to configure your JavaScript files to use an Amazon Cognito unauthenticated role to access the Amazon DynamoDB web service, do the following\.

**To configure credentials to integrate with Amazon Cognito**

1.  Create an Amazon Cognito identity pool that allows unauthenticated identities\. 

   ```
   aws cognito-identity create-identity-pool \
       --identity-pool-name DynamoPool \
       --allow-unauthenticated-identities \
       --output json
   {
       "IdentityPoolId": "us-west-2:12345678-1ab2-123a-1234-a12345ab12",
       "AllowUnauthenticatedIdentities": true,
       "IdentityPoolName": "DynamoPool"
   }
   ```

1.  Copy the following policy into a file named `myCognitoPolicy.json`\. Replace the identity pool ID \(*us\-west\-2:12345678\-1ab2\-123a\-1234\-a12345ab12*\) with your own `IdentityPoolId` obtained in the previous step\.

   ```
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Principal": {
           "Federated": "cognito-identity.amazonaws.com"
         },
         "Action": "sts:AssumeRoleWithWebIdentity",
         "Condition": {
           "StringEquals": {
             "cognito-identity.amazonaws.com:aud": "us-west-2:12345678-1ab2-123a-1234-a12345ab12"
           },
           "ForAnyValue:StringLike": {
             "cognito-identity.amazonaws.com:amr": "unauthenticated"
           }
         }
       }
     ]
   }
   ```

1.  Create an IAM role that assumes the previous policy\. In this way, Amazon Cognito becomes a trusted entity that can assume the `Cognito_DynamoPoolUnauth` role\. 

   ```
   aws iam create-role --role-name Cognito_DynamoPoolUnauth \
   --assume-role-policy-document file://PathToFile/myCognitoPolicy.json --output json
   ```

1.  Grant the `Cognito_DynamoPoolUnauth` role full access to DynamoDB by attaching a managed policy \(`AmazonDynamoDBFullAccess`\)\. 

   ```
   aws iam attach-role-policy --policy-arn arn:aws:iam::aws:policy/AmazonDynamoDBFullAccess \
   --role-name Cognito_DynamoPoolUnauth
   ```
**Note**  
 Alternatively, you can grant fine\-grained access to DynamoDB\. For more information, see [Using IAM policy conditions for fine\-grained access control](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/specifying-conditions.html)\. 

1.  Obtain and copy the IAM role Amazon Resource Name \(ARN\)\.

   ```
   aws iam get-role --role-name Cognito_DynamoPoolUnauth --output json 
   ```

1.  Add the `Cognito_DynamoPoolUnauth` role to the `DynamoPool` identity pool\. The format to specify is `KeyName=string`, where `KeyName` is `unauthenticated` and the string is the role ARN obtained in the previous step\. 

   ```
   aws cognito-identity set-identity-pool-roles \
   --identity-pool-id "us-west-2:12345678-1ab2-123a-1234-a12345ab12" \
   --roles unauthenticated=arn:aws:iam::123456789012:role/Cognito_DynamoPoolUnauth --output json
   ```

1.  Specify the Amazon Cognito credentials in your files\. Modify the `IdentityPoolId` and `RoleArn` accordingly\. 

   ```
   AWS.config.credentials = new AWS.CognitoIdentityCredentials({
   IdentityPoolId: "us-west-2:12345678-1ab2-123a-1234-a12345ab12",
   RoleArn: "arn:aws:iam::123456789012:role/Cognito_DynamoPoolUnauth"
   });
   ```

 You can now run your JavaScript programs against the DynamoDB web service using Amazon Cognito credentials\. For more information, see [Setting credentials in a web browser](https://docs.aws.amazon.com/sdk-for-javascript/latest/developer-guide/setting-credentials-browser.html) in the *AWS SDK for JavaScript Getting Started Guide*\. 