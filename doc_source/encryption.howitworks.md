# Encryption at Rest: How It Works<a name="encryption.howitworks"></a>

Amazon DynamoDB encryption at rest provides an additional layer of data protection by securing your data from unauthorized access to the underlying storage\. Organizational policies, industry or government regulations, and compliance requirements might require the use of encryption at rest to protect your data\. You can use encryption to increase the data security of your applications that are deployed in the cloud\.

With encryption at rest, you can enable encryption for all your DynamoDB data at rest: the data that is persisted in your DynamoDB tables, local secondary indexes, and global secondary indexes\. Encryption at rest encrypts your data using 256\-bit AES encryption, also known as AES\-256 encryption\. It works at the table level and encrypts not only the base table, but also its indexes\.

Encryption at rest automatically integrates with AWS Key Management Service \(AWS KMS\) for managing the single service default key that is used to encrypt your tables\. If a service default key doesn't exist when you create your encrypted DynamoDB table, AWS KMS automatically creates a new key for you\. This key is used with encrypted tables that are created in the future\. AWS KMS combines secure, highly available hardware and software to provide a key management system scaled for the cloud\. 

**Note**  
Amazon DynamoDB can't read your table data unless it has access to the service default key stored in your AWS KMS account\. DynamoDB uses envelope encryption and key hierarchy to encrypt data\. Your AWS KMS encryption key is used to encrypt the root key of this key hierarchy\. For more information, see [How Envelope Encryption Works with Supported AWS Services](http://docs.aws.amazon.com/kms/latest/developerguide/workflow.html)\.

Using the same AWS KMS single service default key that is used to encrypt the table, the following are encrypted:
+ DynamoDB base tables
+ Local secondary indexes
+ Global secondary indexes

 After your data is encrypted, DynamoDB handles decryption of your data transparently with minimal impact on performance\. You don't need to modify your applications to use encryption\. 

**Note**  
 DynamoDB does not call KMS for every single DynamoDB operation\. The key is refreshed once every five minutes per client connection with active traffic\. 