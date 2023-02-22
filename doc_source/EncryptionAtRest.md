# DynamoDB encryption at rest<a name="EncryptionAtRest"></a>

All user data stored in Amazon DynamoDB is fully encrypted at rest\. DynamoDB encryption at rest provides enhanced security by encrypting all your data at rest using encryption keys stored in [AWS Key Management Service \(AWS KMS\)](https://aws.amazon.com/kms/)\. This functionality helps reduce the operational burden and complexity involved in protecting sensitive data\. With encryption at rest, you can build security\-sensitive applications that meet strict encryption compliance and regulatory requirements\.

DynamoDB encryption at rest provides an additional layer of data protection by always securing your data in an encrypted table—including its primary key, local and global secondary indexes, streams, global tables, backups, and DynamoDB Accelerator \(DAX\) clusters whenever the data is stored in durable media\. Organizational policies, industry or government regulations, and compliance requirements often require the use of encryption at rest to increase the data security of your applications\.

Encryption at rest integrates with AWS KMS for managing the encryption keys that are used to encrypt your tables\. For more information on key types and states, see [AWS Key Management Service concepts](https://docs.aws.amazon.com/kms/latest/developerguide/key-state.html#key-state-cmk-type) in the *AWS Key Management Service Developer Guide*\.

When creating a new table, you can choose one of the following AWS KMS key types to encrypt your table\. You can switch between these key types at any time\.
+ **AWS owned key –** Default encryption type\. The key is owned by DynamoDB \(no additional charge\)\.
+ **AWS managed key –** The key is stored in your account and is managed by AWS KMS \(AWS KMS charges apply\)\.
+ **Customer managed key –** The key is stored in your account and is created, owned, and managed by you\. You have full control over the KMS key \(AWS KMS charges apply\)\.

For more information on key types, see [ Customer keys and AWS keys](/kms/latest/developerguide/concepts.html#key-mgmt)\.

**Note**  
When creating a new DAX cluster with encryption at rest enabled, an AWS managed key will be used to encrypt data at rest in the cluster\.
If your table has a sort key, some of the sort keys that mark range boundaries are stored in plaintext in the table metadata\.

When you access an encrypted table, DynamoDB decrypts the table data transparently\. You don't have to change any code or applications to use or manage encrypted tables\. DynamoDB continues to deliver the same single\-digit millisecond latency that you have come to expect, and all DynamoDB queries work seamlessly on your encrypted data\.

You can specify an encryption key when you create a new table or switch the encryption keys on an existing table by using the AWS Management Console, AWS Command Line Interface \(AWS CLI\), or the Amazon DynamoDB API\. To learn how, see [Managing encrypted tables in DynamoDB](encryption.tutorial.md)\.

Encryption at rest using the AWS owned key is offered at no additional charge\. However, AWS KMS charges apply for an AWS managed key and for a customer managed key\. For more information about pricing, see [AWS KMS pricing](https://aws.amazon.com/kms/pricing)\.

DynamoDB encryption at rest is available in all AWS Regions, including the AWS China \(Beijing\) and AWS China \(Ningxia\) Regions and the AWS GovCloud \(US\) Regions\. For more information, see [Encryption at rest: How it works](encryption.howitworks.md) and [DynamoDB encryption at rest usage notes](encryption.usagenotes.md)\.