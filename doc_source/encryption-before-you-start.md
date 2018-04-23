# Before You Begin Using Encryption at Rest<a name="encryption-before-you-start"></a>

Before you enable encryption at rest on an Amazon DynamoDB table, consider the following:
+ When encryption is enabled for a table, all the data stored in that table is encrypted\. You can't encrypt only a subset of items in a table\.
+ Amazon DynamoDB uses a single service default key for encrypting all of your tables\. If this key doesn’t exist, it is created for you\. Service default keys can't be disabled\. 
+ Encryption at rest only encrypts data while it is static \(at rest\) on a persistent storage media\. If data security is a concern for data in transit or data in use, you need to take additional measures:
  + Data\-in\-transit: Protect your data while it is actively moving over a public or a private network by encrypting sensitive data on the client side or using encrypted connections \(HTTPS, SSL, TLS, FTPS, and so on\)\.
  + Data\-in\-use: Protect your data before sending it to DynamoDB using client\-side encryption\.
**Important**  
 Currently, you cannot enable Encryption at Rest for DynamoDB Streams\. If Encryption at Rest is a compliance/regulatory requirement, we recommend turning off DynamoDB Streams for encrypted tables\. 
+  You can use on\-demand backup and restore with encrypted tables, and you can create a backup of an encrypted table\. The table that is restored with this backup has encryption enabled\. For more information, see [On\-Demand Backup and Restore for DynamoDB](BackupRestore.md)\. 