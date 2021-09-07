# DynamoDB Preventative Security Best Practices<a name="best-practices-security-preventative"></a>

The following best practices can help you anticipate and prevent security incidents in Amazon DynamoDB\.

**Encryption at rest**  
DynamoDB encrypts at rest all user data stored in tables, indexes, streams, and backups using encryption keys stored in [AWS Key Management Service \(AWS KMS\)](https://aws.amazon.com/kms/)\. This provides an additional layer of data protection by securing your data from unauthorized access to the underlying storage \.  
You can specify whether DynamoDB should use an AWS owned key \(default encryption type\), an AWS managed key, or a customer managed key to encrypt user data\. For more information, see [Amazon DynamoDB Encryption at Rest](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/EncryptionAtRest.html)\.

**Use IAM roles to authenticate access to DynamoDB**  
For users, applications, and other AWS services to access DynamoDB, they must include valid AWS credentials in their AWS API requests\. You should not store AWS credentials directly in the application or EC2 instance\. These are long\-term credentials that are not automatically rotated, and therefore could have significant business impact if they are compromised\. An IAM role enables you to obtain temporary access keys that can be used to access AWS services and resources\.  
For more information, see [Authentication](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/authentication-and-access-control.html#authentication)\.

**Use IAM policies for DynamoDB base authorization**  
When granting permissions, you decide who is getting them, which DynamoDB APIs they are getting permissions for, and the specific actions you want to allow on those resources\. Implementing least privilege is key in reducing security risk and the impact that can result from errors or malicious intent\.  
Attach permissions policies to IAM identities \(that is, users, groups, and roles\) and thereby grant permissions to perform operations on DynamoDB resources\.  
You can do this by using the following:  
+ [AWS Managed \(predefined\) policies](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/using-identity-based-policies.html#access-policy-examples-aws-managed)
+ [Customer managed policies](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/using-identity-based-policies.html#access-policy-examples-for-sdk-cli)

**Use IAM policy conditions for fine\-grained access control**  
When you grant permissions in DynamoDB, you can specify conditions that determine how a permissions policy takes effect\. Implementing least privilege is key in reducing security risk and the impact that can result from errors or malicious intent\.  
You can specify conditions when granting permissions using an IAM policy\. For example, you can do the following:  
+ Grant permissions to allow users read\-only access to certain items and attributes in a table or a secondary index\.
+ Grant permissions to allow users write\-only access to certain attributes in a table, based upon the identity of that user\.
 For more information, see [Using IAM Policy Conditions for Fine\-Grained Access Control](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/specifying-conditions.html)\.

**Use a VPC endpoint and policies to access DynamoDB**  
If you only require access to DynamoDB from within a virtual private cloud \(VPC\), you should use a VPC endpoint to limit access from only the required VPC\. Doing this prevents that traffic from traversing the open internet and being subject to that environment\.  
Using a VPC endpoint for DynamoDB allows you to control and limit access using the following:  
+ VPC endpoint policies – These policies are applied on the DynamoDB VPC endpoint\. They allow you to control and limit API access to the DynamoDB table\.
+ IAM policies – By using the `aws:sourceVpce` condition on policies attached to IAM users, groups, or roles, you can enforce that all access to the DynamoDB table is via the specified VPC endpoint\.
 For more information, see [Endpoints for Amazon DynamoDB](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints-ddb.html)\.

**Consider client\-side encryption**  
If you store sensitive or confidential data in DynamoDB, you might want to encrypt that data as close as possible to its origin so that your data is protected throughout its lifecycle\. Encrypting your sensitive data in transit and at rest helps ensure that your plaintext data isn’t available to any third party\.  
 The [Amazon DynamoDB Encryption Client](https://docs.aws.amazon.com/dynamodb-encryption-client/latest/devguide/what-is-ddb-encrypt.html) is a software library that helps you protect your table data before you send it to DynamoDB\.  
 At the core of the DynamoDB Encryption Client is an item encryptor that encrypts, signs, verifies, and decrypts table items\. It takes in information about your table items and instructions about which items to encrypt and sign\. It gets the encryption materials and instructions on how to use them from a cryptographic material provider that you select and configure\.