# Amazon DynamoDB Encryption at Rest<a name="EncryptionAtRest"></a>

All user data stored in Amazon DynamoDB is fully encrypted at rest\. DynamoDB encryption at rest provides enhanced security by encrypting all your data at rest using encryption keys stored in [AWS Key Management Service \(AWS KMS\)](https://aws.amazon.com/kms/)\. This functionality helps reduce the operational burden and complexity involved in protecting sensitive data\. With encryption at rest, you can build security\-sensitive applications that meet strict encryption compliance and regulatory requirements\. 

DynamoDB encryption at rest provides an additional layer of data protection by securing your data in the encrypted table, including its primary key, local and global secondary indexes, streams, global tables, backups, and DynamoDB Accelerator \(DAX\) clusters whenever the data is stored in durable media\. Organizational policies, industry or government regulations, and compliance requirements often require the use of encryption at rest to increase the data security of your applications\. 

Encryption at rest integrates with AWS KMS for managing the encryption key that is used to encrypt your tables\. 

 When creating a new table, you can choose one of the following customer master keys \(CMK\) to encrypt your table: 
+ AWS owned CMK – Default encryption type\. The key is owned by DynamoDB \(no additional charge\)\. 
+  AWS managed CMK – The key is stored in your account and is managed by AWS KMS \(AWS KMS charges apply\)\. 

 When you access an encrypted table, DynamoDB decrypts the table data transparently\. You can switch between the AWS owned CMK and AWS managed CMK at any given time\. You don't have to change any code or applications to use or manage encrypted tables\. DynamoDB continues to deliver the same single\-digit millisecond latency that you have come to expect, and all DynamoDB queries work seamlessly on your encrypted data\. 

For more information, see [Encryption at Rest: How It Works](encryption.howitworks.md) and [Encryption at Rest Usage Notes](encryption.usagenotes.md)\. 

To learn how to create an encrypted table or switch the encryption keys on an existing table using the AWS Management Console, AWS Command Line Interface \(AWS CLI\), or the Amazon DynamoDB API, see [Managing Encrypted Tables](encryption.tutorial.md)\.

**Note**  
Encryption at rest is generally available in all commercial AWS Regions and AWS GovCloud \(US\), but not currently supported in the following AWS Regions:   
China \(Beijing\)
China \(Ningxia\)

 Encryption at rest using the AWS owned CMK is offered at no additional cost\. However, AWS KMS charges apply for AWS managed CMK\. For more information about pricing, see [Amazon DynamoDB Pricing](https://aws.amazon.com/dynamodb/pricing)\. 