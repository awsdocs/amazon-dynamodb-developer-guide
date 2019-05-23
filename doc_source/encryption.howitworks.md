# Encryption at Rest: How It Works<a name="encryption.howitworks"></a>

 Amazon DynamoDB encryption at rest encrypts your data using 256\-bit Advanced Encryption Standard \(AES\-256\), which helps secure your data from unauthorized access to the underlying storage\. 

Encryption at rest integrates with AWS Key Management Service \(AWS KMS\) for managing the encryption key that is used to encrypt your tables\. 

When creating a new table or switching the encryption keys on an existing table, you can choose one of the following customer master keys \(CMK\): 
+ AWS owned CMK – Default encryption type\. The key is owned by DynamoDB \(no additional charge\)\. 
+ AWS managed CMK – The key is stored in your account and is managed by AWS KMS \(AWS KMS charges apply\)\. 

The AWS managed CMK provides these additional features:
+  You can view the CMK and its key policy\. \(You cannot change the key policy\.\) 
+  You can audit the encryption and decryption of your DynamoDB table by examining the DynamoDB API calls to AWS KMS using AWS CloudTrail\. 

## AWS Managed CMK<a name="service-default-kms"></a>

Encryption at rest automatically integrates with AWS KMS for managing the AWS managed CMK for DynamoDB \(`aws/dynamodb`\) that is used to encrypt your tables\. If an AWS managed CMK doesn't exist when you create your encrypted DynamoDB table, AWS KMS automatically creates a new key for you\. This key is used with encrypted tables that are created in the future\. AWS KMS combines secure, highly available hardware and software to provide a key management system scaled for the cloud\. 

**Note**  
Amazon DynamoDB can't read your table data unless it has access to the AWS managed CMK stored in your AWS KMS account\. DynamoDB uses envelope encryption and key hierarchy to encrypt data\. Your AWS KMS encryption key is used to encrypt the root key of this key hierarchy\. For more information, see [Envelope Encryption](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#enveloping) in the *AWS Key Management Service Developer Guide*\. 

 DynamoDB doesn't call AWS KMS for every single DynamoDB operation\. The key is refreshed once every 5 minutes per client connection with active traffic\. 

Ensure that you have configured the SDK to reuse connections\. Otherwise you will experience latencies from DynamoDB having to re\-establish new AWS KMS cache entries for each DynamoDB operation, and potentially have to face higher AWS KMS and CloudTrail costs\. For example, to do this using the Node\.js SDK, you can create a new HTTPS agent with `keepAlive` turned on\. For more information, see [Configuring maxSockets in Node\.js](https://docs.aws.amazon.com/sdk-for-javascript/v2/developer-guide/node-configuring-maxsockets.html) in the *AWS SDK for JavaScript Developer Guide*\. 

 For more information about managing permissions of the AWS managed CMK, see [Authorizing Use of the Service Default Key](https://docs.aws.amazon.com/kms/latest/developerguide/services-dynamodb.html#dynamodb-authz)\. 

 You can use AWS CloudTrail and Amazon CloudWatch Logs to track the requests that DynamoDB sends to AWS KMS on your behalf\. For more information, see [Monitoring DynamoDB Interaction with AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/services-dynamodb.html#dynamodb-cmk-fail)\. 