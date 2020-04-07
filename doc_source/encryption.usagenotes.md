# DynamoDB Encryption at Rest Usage Notes<a name="encryption.usagenotes"></a>

Consider the following when you are using encryption at rest in Amazon DynamoDB\.

## All Table Data Is Encrypted<a name="encryption.usagenotes.tabledata"></a>

Server\-side encryption at rest is enabled on all DynamoDB table data and cannot be disabled\. You cannot encrypt only a subset of items in a table\. DynamoDB has encrypted all existing tables that were previously unencrypted by using the AWS owned customer master key \(CMK\)\.

Encryption at rest only encrypts data while it is static \(at rest\) on a persistent storage media\. If data security is a concern for data in transit or data in use, you might need to take additional measures:
+ Data in transit: All your data in DynamoDB is encrypted in transit \(except the data in DAX\)\. By default, communications to and from DynamoDB use the HTTPS protocol, which protects network traffic by using Secure Sockets Layer \(SSL\)/Transport Layer Security \(TLS\) encryption\.
+ Data in use: Protect your data before sending it to DynamoDB using client\-side encryption\. For more information, see [Client\-Side and Server\-Side Encryption](https://docs.aws.amazon.com/dynamodb-encryption-client/latest/devguide/client-server-side.html) in the *Amazon DynamoDB Encryption Client Developer Guide*\.

You can use streams with encrypted tables\. DynamoDB streams are always encrypted with a table\-level encryption key\. For more information, see [Capturing Table Activity with DynamoDB Streams](Streams.md)\.

DynamoDB backups are encrypted, and the table that is restored from a backup also has encryption enabled\. You can use the AWS owned CMK, AWS managed CMK, or customer managed CMK to encrypt your backup data\. For more information, see [On\-Demand Backup and Restore for DynamoDB](BackupRestore.md)\.

Local secondary indexes and global secondary indexes are encrypted using the same key as the base table\.

Global tables can be encrypted using an AWS owned CMK or AWS managed CMK\.

## Encryption Types<a name="encryption.usagenotes.encryptiontypes"></a>

On the AWS Management Console, the encryption type is `KMS` when you use the AWS managed CMK or customer managed CMK to encrypt your data\. The encryption type is `DEFAULT` when you use the AWS owned CMK\. In the Amazon DynamoDB API, the encryption type is `KMS` when you use the AWS managed CMK or customer managed CMK\. In the absence of encryption type, your data is encrypted using the AWS owned CMK\. You can switch between the AWS owned CMK, AWS managed CMK, and customer managed CMK at any given time\. You can use the console, the AWS Command Line Interface \(AWS CLI\), or the Amazon DynamoDB API to switch the encryption keys\.

Note the following limitations when using customer managed CMKs:
+ You cannot use a customer managed CMK to encrypt global tables\. For more information, see [Global Tables: Multi\-Region Replication with DynamoDB](GlobalTables.md)\.
+ You cannot use a customer managed CMK with DynamoDB Accelerator \(DAX\) clusters\. For more information, see [DAX Encryption at Rest](DAXEncryptionAtRest.md)\.
+ You can use a customer managed CMK to encrypt tables that use transactions\. However, during transaction propagation, data is encrypted using an AWS owned CMK\. Data at rest is still encrypted using your customer managed CMK\.
+ You can use a customer managed CMK to encrypt tables that use Contributor Insights\. However, data that is transmitted to Amazon CloudWatch is encrypted with an AWS owned CMK\.
+ If you disable your customer managed CMK or schedule it for deletion, any data in DynamoDB Streams is still subject to a 24\-hour lifetime\. Any unretrieved activity data is eligible for trimming when it is older than 24 hours\.
+ If you disable your customer managed CMK or schedule it for deletion, Time to Live \(TTL\) deletes continue for 30 minutes\. These TTL deletes continue to be emitted to DynamoDB Streams and are subject to the standard trimming/retention interval\.