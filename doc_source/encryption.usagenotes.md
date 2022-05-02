# DynamoDB Encryption at Rest Usage Notes<a name="encryption.usagenotes"></a>

Consider the following when you are using encryption at rest in Amazon DynamoDB\.

## All Table Data Is Encrypted<a name="encryption.usagenotes.tabledata"></a>

Server\-side encryption at rest is enabled on all DynamoDB table data and cannot be disabled\. You cannot encrypt only a subset of items in a table\. DynamoDB has encrypted all existing tables that were previously unencrypted by using the AWS owned key\.

Encryption at rest only encrypts data while it is static \(at rest\) on a persistent storage media\. If data security is a concern for data in transit or data in use, you might need to take additional measures:
+ Data in transit: All your data in DynamoDB is encrypted in transit \(except the data in DAX\)\. By default, communications to and from DynamoDB use the HTTPS protocol, which protects network traffic by using Secure Sockets Layer \(SSL\)/Transport Layer Security \(TLS\) encryption\.
+ Data in use: Protect your data before sending it to DynamoDB using client\-side encryption\. For more information, see [Client\-Side and Server\-Side Encryption](https://docs.aws.amazon.com/dynamodb-encryption-client/latest/devguide/client-server-side.html) in the *Amazon DynamoDB Encryption Client Developer Guide*\.

You can use streams with encrypted tables\. DynamoDB streams are always encrypted with a table\-level encryption key\. For more information, see [Change Data Capture for DynamoDB Streams](Streams.md)\.

DynamoDB backups are encrypted, and the table that is restored from a backup also has encryption enabled\. You can use the AWS owned key, AWS managed key, or customer managed key to encrypt your backup data\. For more information, see [Using On\-Demand Backup and Restore for DynamoDB](BackupRestore.md)\.

Local secondary indexes and global secondary indexes are encrypted using the same key as the base table\.

## Encryption Types<a name="encryption.usagenotes.encryptiontypes"></a>

**Note**  
Customer Managed Key \(CMK\) is not supported in Global Table Version 2017\. If you want to use a CMK in a DynamoDB Global Table, you need to upgrade the table to Global Table Version 2019 and then enable it\.

On the AWS Management Console, the encryption type is `KMS` when you use the AWS managed key or customer managed key to encrypt your data\. The encryption type is `DEFAULT` when you use the AWS owned key\. In the Amazon DynamoDB API, the encryption type is `KMS` when you use the AWS managed key or customer managed key\. In the absence of encryption type, your data is encrypted using the AWS owned key\. You can switch between the AWS owned key, AWS managed key, and customer managed key at any given time\. You can use the console, the AWS Command Line Interface \(AWS CLI\), or the Amazon DynamoDB API to switch the encryption keys\.

Note the following limitations when using customer managed keys:
+ You cannot use a customer managed key with DynamoDB Accelerator \(DAX\) clusters\. For more information, see [DAX Encryption at Rest](DAXEncryptionAtRest.md)\.
+ You can use a customer managed key to encrypt tables that use transactions\. However, to ensure durability for propagation of transactions, a copy of the transaction request is temporarily stored by the service and encrypted using an AWS owned key\. Committed data in your tables and secondary indexes is always encrypted at rest using your customer managed key\.
+ You can use a customer managed key to encrypt tables that use Contributor Insights\. However, data that is transmitted to Amazon CloudWatch is encrypted with an AWS owned key\.
+ When you transition to a new customer managed key, be sure to keep the original key enabled until the process is complete\. AWS will still need the original key to decrypt the data before encrypting it with the new key\. The process will be complete when the table's SSEDescription Status is ENABLED and the KMSMasterKeyArn of the new customer managed key is displayed\. At this point the original key can be disabled or scheduled for deletion\.
+ Once the new customer managed key is displayed, the table and any new on\-demand backups are encrypted with the new key\.
+ Any existing on\-demand backups remain encrypted with the customer managed key that was used when those backups were created\. You will need that same key to restore those backups\. You can identify the key for the period when each backup was created by using the DescribeBackup API to view that backup's SSEDescription\.
+ If you disable your customer managed key or schedule it for deletion, any data in DynamoDB Streams is still subject to a 24\-hour lifetime\. Any unretrieved activity data is eligible for trimming when it is older than 24 hours\.
+ If you disable your customer managed key or schedule it for deletion, Time to Live \(TTL\) deletes continue for 30 minutes\. These TTL deletes continue to be emitted to DynamoDB Streams and are subject to the standard trimming/retention interval\.