# DynamoDB encryption at rest usage notes<a name="encryption.usagenotes"></a>

Consider the following when you are using encryption at rest in Amazon DynamoDB\.

## All table data is encrypted<a name="encryption.usagenotes.tabledata"></a>

Server\-side encryption at rest is enabled on all DynamoDB table data and cannot be disabled\. You cannot encrypt only a subset of items in a table\. DynamoDB has encrypted all existing tables that were previously unencrypted by using the AWS owned key\.

Encryption at rest only encrypts data while it is static \(at rest\) on a persistent storage media\. If data security is a concern for data in transit or data in use, you might need to take additional measures:
+ Data in transit: All your data in DynamoDB is encrypted in transit \(except the data in DAX\)\. By default, communications to and from DynamoDB use the HTTPS protocol, which protects network traffic by using Secure Sockets Layer \(SSL\)/Transport Layer Security \(TLS\) encryption\.
+ Data in use: Protect your data before sending it to DynamoDB using client\-side encryption\. For more information, see [Client\-side and server\-side encryption](https://docs.aws.amazon.com/dynamodb-encryption-client/latest/devguide/client-server-side.html) in the *Amazon DynamoDB Encryption Client Developer Guide*\.

You can use streams with encrypted tables\. DynamoDB streams are always encrypted with a table\-level encryption key\. For more information, see [Change data capture for DynamoDB Streams](Streams.md)\.

DynamoDB backups are encrypted, and the table that is restored from a backup also has encryption enabled\. You can use the AWS owned key, AWS managed key, or customer managed key to encrypt your backup data\. For more information, see [Using On\-Demand backup and restore for DynamoDB](BackupRestore.md)\.

Local secondary indexes and global secondary indexes are encrypted using the same key as the base table\.

## Encryption types<a name="encryption.usagenotes.encryptiontypes"></a>

**Note**  
Customer managed keys are not supported in Global Table Version 2017\. If you want to use a customer managed key in a DynamoDB Global Table, you need to upgrade the table to Global Table Version 2019 and then enable it\.

On the AWS Management Console, the encryption type is `KMS` when you use the AWS managed key or customer managed key to encrypt your data\. The encryption type is `DEFAULT` when you use the AWS owned key\. In the Amazon DynamoDB API, the encryption type is `KMS` when you use the AWS managed key or customer managed key\. In the absence of encryption type, your data is encrypted using the AWS owned key\. You can switch between the AWS owned key, AWS managed key, and customer managed key at any given time\. You can use the console, the AWS Command Line Interface \(AWS CLI\), or the Amazon DynamoDB API to switch the encryption keys\.

Note the following limitations when using customer managed keys:
+ You cannot use a customer managed key with DynamoDB Accelerator \(DAX\) clusters\. For more information, see [DAX encryption at rest](DAXEncryptionAtRest.md)\.
+ You can use a customer managed key to encrypt tables that use transactions\. However, to ensure durability for propagation of transactions, a copy of the transaction request is temporarily stored by the service and encrypted using an AWS owned key\. Committed data in your tables and secondary indexes is always encrypted at rest using your customer managed key\.
+ You can use a customer managed key to encrypt tables that use Contributor Insights\. However, data that is transmitted to Amazon CloudWatch is encrypted with an AWS owned key\.
+ When you transition to a new customer managed key, be sure to keep the original key enabled until the process is complete\. AWS will still need the original key to decrypt the data before encrypting it with the new key\. The process will be complete when the table's SSEDescription Status is ENABLED and the KMSMasterKeyArn of the new customer managed key is displayed\. At this point the original key can be disabled or scheduled for deletion\.
+ Once the new customer managed key is displayed, the table and any new on\-demand backups are encrypted with the new key\.
+ Any existing on\-demand backups remain encrypted with the customer managed key that was used when those backups were created\. You will need that same key to restore those backups\. You can identify the key for the period when each backup was created by using the DescribeBackup API to view that backup's SSEDescription\.
+ If you disable your customer managed key or schedule it for deletion, any data in DynamoDB Streams is still subject to a 24\-hour lifetime\. Any unretrieved activity data is eligible for trimming when it is older than 24 hours\.
+ If you disable your customer managed key or schedule it for deletion, Time to Live \(TTL\) deletes continue for 30 minutes\. These TTL deletes continue to be emitted to DynamoDB Streams and are subject to the standard trimming/retention interval\.

  For more information, see [ enabling keys](/kms/latest/developerguide/enabling-keys.html) and [ deleting keys\.](/kms/latest/developerguide/deleting-keys.html) 

## Using KMS keys and data keys<a name="dynamodb-kms"></a>

The DynamoDB encryption at rest feature uses an AWS KMS key and a hierarchy of data keys to protect your table data\. DynamoDB uses the same key hierarchy to protect DynamoDB streams, global tables, and backups when they are written to durable media\.

We recommend that you plan your encryption strategy before implementing your table in DynamoDB\. If you store sensitive or confidential data in DynamoDB, consider including client\-side encryption in your plan\. This way you can encrypt data as close as possible to its origin, and ensure its protection throughout its lifecycle\. For more information see the [DynamoDB encryption client](https://docs.aws.amazon.com/dynamodb-encryption-client/latest/devguide/what-is-ddb-encrypt.html) documentation\.

**AWS KMS key**  
Encryption at rest protects your DynamoDB tables under an AWS KMS key\. By default, DynamoDB uses an [AWS owned key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#aws-owned-cmk), a multi\-tenant encryption key that is created and managed in a DynamoDB service account\. But you can encrypt your DynamoDB tables under a [customer managed key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#customer-cmk) for DynamoDB \(`aws/dynamodb`\) in your AWS account\. You can select a different KMS key for each table\. The KMS key you select for a table is also used to encrypt its local and global secondary indexes, streams, and backups\.  
You select the KMS key for a table when you create or update the table\. You can change the KMS key for a table at any time, either in the DynamoDB console or by using the [UpdateTable](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_UpdateTable.html) operation\. The process of switching keys is seamless and does not require downtime or degrade service\.  
DynamoDB supports only [symmetric KMS keys](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#symmetric-cmks)\. You cannot use an [asymmetric KMS key](https://docs.aws.amazon.com/kms/latest/developerguide/symmetric-asymmetric.html#asymmetric-cmks) to encrypt your DynamoDB tables\. 
Use a customer managed key to get the following features:  
+ You create and manage the KMS key, including setting the [key policies](https://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html), [IAM policies](https://docs.aws.amazon.com/kms/latest/developerguide/iam-policies.html) and [grants](https://docs.aws.amazon.com/kms/latest/developerguide/grants.html) to control access to the KMS key\. You can [enable and disable](https://docs.aws.amazon.com/kms/latest/developerguide/enabling-keys.html) the KMS key, enable and disable [automatic key rotation](https://docs.aws.amazon.com/kms/latest/developerguide/rotate-keys.html), and [delete the KMS key](https://docs.aws.amazon.com/kms/latest/developerguide/deleting-keys.html) when it is no longer in use\.
+ You can use a customer managed key with [imported key material](https://docs.aws.amazon.com/kms/latest/developerguide/importing-keys.html) or a customer managed key in a [custom key store](https://docs.aws.amazon.com/kms/latest/developerguide/custom-key-store-overview.html) that you own and manage\. 
+ You can audit the encryption and decryption of your DynamoDB table by examining the DynamoDB API calls to AWS KMS in [AWS CloudTrail logs](https://docs.aws.amazon.com/kms/latest/developerguide/services-dynamodb.html#dynamodb-cmk-trail)\.
Use the AWS managed key if you need any of the following features:  
+ You can [view the KMS key](https://docs.aws.amazon.com/kms/latest/developerguide/viewing-keys.html) and [view its key policy](https://docs.aws.amazon.com/kms/latest/developerguide/key-policy-viewing.html)\. \(You cannot change the key policy\.\)
+ You can audit the encryption and decryption of your DynamoDB table by examining the DynamoDB API calls to AWS KMS in [AWS CloudTrail logs](https://docs.aws.amazon.com/kms/latest/developerguide/services-dynamodb.html#dynamodb-cmk-trail)\.
However, the AWS owned key is free of charge and its use does not count against [AWS KMS resource or request quotas](https://docs.aws.amazon.com/kms/latest/developerguide/limits.html)\. Customer managed keys and AWS managed keys [incur a charge](https://aws.amazon.com/kms/pricing/) for each API call and AWS KMS quotas apply to these KMS keys\.

**Table keys**  
DynamoDB uses the KMS key for the table to [generate](kms/latest/APIReference/API_GenerateDataKey.html) and encrypt a unique [data key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#data-keys) for the table, known as the *table key*\. The table key persists for the lifetime of the encrypted table\.   
The table key is used as a key encryption key\. DynamoDB uses this table key to protect data encryption keys that are used to encrypt the table data\. DynamoDB generates a unique data encryption key for each underlying structure in a table, but multiple table items might be protected by the same data encryption key\.  

![\[Encrypting a DynamoDB table with encryption at rest\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/service-ddb-encrypt.png)
When you first access an encrypted table, DynamoDB sends a request to AWS KMS to use the KMS key to decrypt the table key\. Then, it uses the plaintext table key to decrypt the data encryption keys, and uses the plaintext data encryption keys to decrypt table data\.  
DynamoDB stores and uses the table key and data encryption keys outside of AWS KMS\. It protects all keys with [Advanced Encryption Standard](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) \(AES\) encryption and 256\-bit encryption keys\. Then, it stores the encrypted keys with the encrypted data so they are available to decrypt the table data on demand\.  
If you change the KMS key for your table, DynamoDB generates a new table key\. Then, it uses the new table key to re\-encrypt the data encryption keys\.

**Table key caching**  
To avoid calling AWS KMS for every DynamoDB operation, DynamoDB caches the plaintext table keys for each connection in memory\. If DynamoDB gets a request for the cached table key after five minutes of inactivity, it sends a new request to AWS KMS to decrypt the table key\. This call will capture any changes made to the access policies of the KMS key in AWS KMS or AWS Identity and Access Management \(IAM\) since the last request to decrypt the table key\.

## Authorizing use of your KMS key<a name="dynamodb-kms-authz"></a>

If you use a [customer managed key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#customer-cmk) or the [AWS managed key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#aws-managed-cmk) in your account to protect your DynamoDB table, the policies on that KMS key must give DynamoDB permission to use it on your behalf\. The authorization context on the AWS managed key for DynamoDB includes its key policy and grants that delegate the permissions to use it\. 

You have full control over the policies and grants on a customer managed key Because the AWS managed key is in your account, you can view its policies and grants\. But, because it is managed by AWS, you cannot change the policies\.

DynamoDB does not need additional authorization to use the default [AWS owned key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#kms_keys) to protect the DynamoDB tables in your AWS account\.

**Topics**
+ [Key policy for an AWS managed key](#dynamodb-policies)
+ [Key policy for a customer managed key](#dynamodb-customer-cmk-policy)
+ [Using grants to authorize DynamoDB](#dynamodb-grants)

### Key policy for an AWS managed key<a name="dynamodb-policies"></a>

When DynamoDB uses the [AWS managed key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#aws-managed-cmk) for DynamoDB \(`aws/dynamodb`\) in cryptographic operations, it does so on behalf of the user who is accessing the [DynamoDB resource](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/access-control-overview.html)\. The key policy on the AWS managed key gives all users in the account permission to use the AWS managed key for specified operations\. But permission is granted only when DynamoDB makes the request on the user's behalf\. The [ViaService condition](https://docs.aws.amazon.com/kms/latest/developerguide/policy-conditions.html#conditions-kms-via-service) in the key policy does not allow any user to use the AWS managed key unless the request originates with the DynamoDB service\.

This key policy, like the policies of all AWS managed keys, is established by AWS\. You cannot change it, but you can view it at any time\. For details, see [Viewing a key policy](https://docs.aws.amazon.com/kms/latest/developerguide/key-policy-viewing.html)\.

The policy statements in the key policy have the following effect:
+ Allow users in the account to use the AWS managed key for DynamoDB in cryptographic operations when the request comes from DynamoDB on their behalf\. The policy also allows users to [create grants](https://docs.aws.amazon.com/kms/latest/developerguide/services-dynamodb.html#dynamodb-grants) for the KMS key\.
+ Allows authorized IAM identities in the account to view the properties of the AWS managed key for DynamoDB and to [revoke the grant](https://docs.aws.amazon.com/kms/latest/APIReference/API_RevokeGrant.html) that allows DynamoDB to use the KMS key\. DynamoDB uses [grants](https://docs.aws.amazon.com/kms/latest/developerguide/services-dynamodb.html#dynamodb-grants) for ongoing maintenance operations\.
+ Allows DynamoDB to perform read\-only operations to find the AWS managed key for DynamoDB in your account\.

```
{
  "Version" : "2012-10-17",
  "Id" : "auto-dynamodb-1",
  "Statement" : [ {
    "Sid" : "Allow access through Amazon DynamoDB for all principals in the account that are authorized to use Amazon DynamoDB",
    "Effect" : "Allow",
    "Principal" : {
      "AWS" : "*"
    },
    "Action" : [ "kms:Encrypt", "kms:Decrypt", "kms:ReEncrypt*", "kms:GenerateDataKey*", "kms:CreateGrant", "kms:DescribeKey" ],
    "Resource" : "*",
    "Condition" : {
      "StringEquals" : {
        "kms:CallerAccount" : "111122223333",
        "kms:ViaService" : "dynamodb.us-west-2.amazonaws.com"
      }
    }
  }, {
    "Sid" : "Allow direct access to key metadata to the account",
    "Effect" : "Allow",
    "Principal" : {
      "AWS" : "arn:aws:iam::111122223333:root"
    },
    "Action" : [ "kms:Describe*", "kms:Get*", "kms:List*", "kms:RevokeGrant" ],
    "Resource" : "*"
  }, {
    "Sid" : "Allow DynamoDB Service with service principal name dynamodb.amazonaws.com to describe the key directly",
    "Effect" : "Allow",
    "Principal" : {
      "Service" : "dynamodb.amazonaws.com"
    },
    "Action" : [ "kms:Describe*", "kms:Get*", "kms:List*" ],
    "Resource" : "*"
  } ]
}
```

### Key policy for a customer managed key<a name="dynamodb-customer-cmk-policy"></a>

When you select a [customer managed key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#customer-cmk) to protect a DynamoDB table, DynamoDB gets permission to use the KMS key on behalf of the principal who makes the selection\. That principal, a user or role, must have the permissions on the KMS key that DynamoDB requires\. You can provide these permissions in a [key policy](https://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html), an [IAM policy](https://docs.aws.amazon.com/kms/latest/developerguide/iam-policies.html), or a [grant](https://docs.aws.amazon.com/kms/latest/developerguide/grants.html)\.

At a minimum, DynamoDB requires the following permissions on a customer managed key:
+ [kms:Encrypt](https://docs.aws.amazon.com/kms/latest/APIReference/API_Encrypt.html)
+ [kms:Decrypt](https://docs.aws.amazon.com/kms/latest/APIReference/API_Decrypt.html)
+ [kms:ReEncrypt](https://docs.aws.amazon.com/kms/latest/APIReference/API_ReEncrypt.html)\* \(for kms:ReEncryptFrom and kms:ReEncryptTo\)
+ kms:GenerateDataKey\* \(for [kms:GenerateDataKey](https://docs.aws.amazon.com/kms/latest/APIReference/API_GenerateDataKey.html) and [kms:GenerateDataKeyWithoutPlaintext](https://docs.aws.amazon.com/kms/latest/APIReference/API_GenerateDataKeyWithoutPlaintext.html)\)
+ [kms:DescribeKey](https://docs.aws.amazon.com/kms/latest/APIReference/API_DescribeKey.html)
+ [kms:CreateGrant](https://docs.aws.amazon.com/kms/latest/APIReference/API_CreateGrant.html)

For example, the following example key policy provides only the required permissions\. The policy has the following effects:
+ Allows DynamoDB to use the KMS key in cryptographic operations and create grants, but only when it is acting on behalf of principals in the account who have permission to use DynamoDB\. If the principals specified in the policy statement don't have permission to use DynamoDB, the call fails, even when it comes from the DynamoDB service\. 
+ The [kms:ViaService](https://docs.aws.amazon.com/kms/latest/developerguide/policy-conditions.html#conditions-kms-via-service) condition key allows the permissions only when the request comes from DynamoDB on behalf of the principals listed in the policy statement\. These principals can't call these operations directly\. Note that the `kms:ViaService` value, `dynamodb.*.amazonaws.com`, has an asterisk \(\*\) in the Region position\. DynamoDB requires the permission to be independent of any particular AWS Region so it can make cross\-Region calls to support [DynamoDB global tables](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GlobalTables.html)\.
+ Gives the KMS key administrators \(users who can assume the `db-team` role\) read\-only access to the KMS key and permission to revoke grants, including the [grants that DynamoDB requires](#dynamodb-grants) to protect the table\.

Before using an example key policy, replace the example principals with actual principals from your AWS account\.

```
{
  "Id": "key-policy-dynamodb",
  "Version":"2012-10-17",
  "Statement": [
    {
      "Sid" : "Allow access through Amazon DynamoDB for all principals in the account that are authorized to use Amazon DynamoDB",
      "Effect": "Allow",
      "Principal": {"AWS": "arn:aws:iam::111122223333:user/db-lead"},
      "Action": [
        "kms:Encrypt",
        "kms:Decrypt",
        "kms:ReEncrypt*",
        "kms:GenerateDataKey*",
        "kms:DescribeKey",
        "kms:CreateGrant"
      ],
      "Resource": "*",      
      "Condition": { 
         "StringLike": {
           "kms:ViaService" : "dynamodb.*.amazonaws.com"
         }
      }
    },
    {
      "Sid":  "Allow administrators to view the KMS key and revoke grants",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::111122223333:role/db-team"
       },
      "Action": [
        "kms:Describe*",
        "kms:Get*",
        "kms:List*",
        "kms:RevokeGrant"
      ],
      "Resource": "*"
    }
  ]
}
```

### Using grants to authorize DynamoDB<a name="dynamodb-grants"></a>

In addition to key policies, DynamoDB uses grants to set permissions on a customer managed key or the AWS managed key for DynamoDB \(`aws/dynamodb`\)\. To view the grants on a KMS key in your account, use the [ListGrants](https://docs.aws.amazon.com/kms/latest/APIReference/API_ListGrants.html) operation\. DynamoDB does not need grants, or any additional permissions, to use the [AWS owned key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#aws-owned-cmk) to protect your table\.

DynamoDB uses the grant permissions when it performs background system maintenance and continuous data protection tasks\. It also uses grants to generate [table keys](https://docs.aws.amazon.com/kms/latest/developerguide/services-dynamodb.html#dynamodb-encrypt)\.

Each grant is specific to a table\. If the account includes multiple tables encrypted under the same KMS key, there is a grant of each type for each table\. The grant is constrained by the [DynamoDB encryption context](#dynamodb-encryption-context), which includes the table name and the AWS account ID, and it includes permission to the [retire the grant](https://docs.aws.amazon.com/kms/latest/APIReference/API_RetireGrant.html) if it is no longer needed\. 

To create the grants, DynamoDB must have permission to call `CreateGrant` on behalf of the user who created the encrypted table\. For AWS managed keys, DynamoDB gets `kms:CreateGrant` permission from the [key policy](#dynamodb-policies), which allows account users to call [CreateGrant](https://docs.aws.amazon.com/kms/latest/APIReference/API_CreateGrant.html) on the KMS key only when DynamoDB makes the request on an authorized user's behalf\. 

The key policy can also allow the account to [revoke the grant](https://docs.aws.amazon.com/kms/latest/APIReference/API_RevokeGrant.html) on the KMS key\. However, if you revoke the grant on an active encrypted table, DynamoDB will not be able to protect and maintain the table\.

## DynamoDB encryption context<a name="dynamodb-encryption-context"></a>

An [encryption context](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#encrypt_context) is a set of key–value pairs that contain arbitrary nonsecret data\. When you include an encryption context in a request to encrypt data, AWS KMS cryptographically binds the encryption context to the encrypted data\. To decrypt the data, you must pass in the same encryption context\. 

DynamoDB uses the same encryption context in all AWS KMS cryptographic operations\. If you use a [customer managed key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#customer-cmk) or an [AWS managed key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#aws-managed-cmk) to protect your DynamoDB table, you can use the encryption context to identify use of the KMS key in audit records and logs\. It also appears in plaintext in logs, such as [AWS CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html) and [Amazon CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html)\. 

The encryption context can also be used as a condition for authorization in policies and grants\. DynamoDB uses the encryption context to constrain the [grants](#dynamodb-grants) that allow access to the customer managed key or AWS managed key in your account and region\.

In its requests to AWS KMS, DynamoDB uses an encryption context with two key–value pairs\.

```
"encryptionContextSubset": {
    "aws:dynamodb:tableName": "Books"
    "aws:dynamodb:subscriberId": "111122223333"
}
```
+ **Table** – The first key–value pair identifies the table that DynamoDB is encrypting\. The key is `aws:dynamodb:tableName`\. The value is the name of the table\.

  ```
  "aws:dynamodb:tableName": "<table-name>"
  ```

  For example:

  ```
  "aws:dynamodb:tableName": "Books"
  ```
+ **Account** – The second key–value pair identifies the AWS account\. The key is `aws:dynamodb:subscriberId`\. The value is the account ID\.

  ```
  "aws:dynamodb:subscriberId": "<account-id>"
  ```

  For example:

  ```
  "aws:dynamodb:subscriberId": "111122223333"
  ```

## Monitoring DynamoDB interaction with AWS KMS<a name="dynamodb-cmk-trail"></a>

If you use a [customer managed key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#customer-cmk) or an [AWS managed key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#aws-managed-cmk) to protect your DynamoDB tables, you can use AWS CloudTrail logs to track the requests that DynamoDB sends to AWS KMS on your behalf\.

The `GenerateDataKey`, `Decrypt`, and `CreateGrant` requests are discussed in this section\. In addition, DynamoDB uses a [DescribeKey](https://docs.aws.amazon.com/kms/latest/APIReference/API_DescribeKey.html) operation to determine whether the KMS key you selected exists in the account and region\. It also uses a [RetireGrant](https://docs.aws.amazon.com/kms/latest/APIReference/API_RetireGrant.html) operation to remove a grant when you delete a table\. 

**GenerateDataKey**  
When you enable encryption at rest on a table, DynamoDB creates a unique table key\. It sends a *[GenerateDataKey](https://docs.aws.amazon.com/kms/latest/APIReference/API_GenerateDataKey.html)* request to AWS KMS that specifies the KMS key for the table\.   
The event that records the `GenerateDataKey` operation is similar to the following example event\. The user is the DynamoDB service account\. The parameters include the Amazon Resource Name \(ARN\) of the KMS key, a key specifier that requires a 256\-bit key, and the [encryption context](#dynamodb-encryption-context) that identifies the table and the AWS account\.  

```
{
    "eventVersion": "1.05",
    "userIdentity": {
        "type": "AWSService", 
        "invokedBy": "dynamodb.amazonaws.com" 
    },
    "eventTime": "2018-02-14T00:15:17Z",
    "eventSource": "kms.amazonaws.com",
    "eventName": "GenerateDataKey",
    "awsRegion": "us-west-2",
    "sourceIPAddress": "dynamodb.amazonaws.com",
    "userAgent": "dynamodb.amazonaws.com",
    "requestParameters": {
        "encryptionContext": {
            "aws:dynamodb:tableName": "Services",
            "aws:dynamodb:subscriberId": "111122223333"
        }, 
        "keySpec": "AES_256", 
        "keyId": ""
    }, 
    "responseElements": null,
    "requestID": "229386c1-111c-11e8-9e21-c11ed5a52190",
    "eventID": "e3c436e9-ebca-494e-9457-8123a1f5e979",
    "readOnly": true,
    "resources": [
        {
            "ARN": "",
            "accountId": "111122223333",
            "type": "AWS::KMS::Key" 
        } 
    ],
    "eventType": "AwsApiCall",
    "recipientAccountId": "111122223333",
    "sharedEventID": "bf915fa6-6ceb-4659-8912-e36b69846aad"
}
```

**Decrypt**  
When you access an encrypted DynamoDB table, DynamoDB needs to decrypt the table key so that it can decrypt the keys below it in the hierarchy\. It then decrypts the data in the table\. To decrypt the table key\. DynamoDB sends a [Decrypt](https://docs.aws.amazon.com/kms/latest/APIReference/API_Decrypt.html) request to AWS KMS that specifies the KMS key for the table\.  
The event that records the `Decrypt` operation is similar to the following example event\. The user is the principal in your AWS account who is accessing the table\. The parameters include the encrypted table key \(as a ciphertext blob\) and the [encryption context](#dynamodb-encryption-context) that identifies the table and the AWS account\. AWS KMS derives the ID of the KMS key from the ciphertext\.   

```
{
    "eventVersion": "1.05",
    "userIdentity": {
        "type": "AssumedRole",
        "principalId": "AROAIGDTESTANDEXAMPLE:user01",
        "arn": "arn:aws:sts::111122223333:assumed-role/Admin/user01",
        "accountId": "111122223333",
        "accessKeyId": "AKIAIOSFODNN7EXAMPLE",
        "sessionContext": {
            "attributes": {
                "mfaAuthenticated": "false", 
                "creationDate": "2018-02-14T16:42:15Z"
            },
            "sessionIssuer": {
                "type": "Role",
                "principalId": "AROAIGDT3HGFQZX4RY6RU",
                "arn": "arn:aws:iam::111122223333:role/Admin",
                "accountId": "111122223333",
                "userName": "Admin" 
            }
        },
        "invokedBy": "dynamodb.amazonaws.com"
    },
    "eventTime": "2018-02-14T16:42:39Z",
    "eventSource": "kms.amazonaws.com",
    "eventName": "Decrypt",
    "awsRegion": "us-west-2",
    "sourceIPAddress": "dynamodb.amazonaws.com",
    "userAgent": "dynamodb.amazonaws.com",
    "requestParameters": 
    {
        "encryptionContext":
        {
            "aws:dynamodb:tableName": "Books",
            "aws:dynamodb:subscriberId": "111122223333" 
        }
    }, 
    "responseElements": null, 
    "requestID": "11cab293-11a6-11e8-8386-13160d3e5db5",
    "eventID": "b7d16574-e887-4b5b-a064-bf92f8ec9ad3", 
    "readOnly": true, 
    "resources": [ 
        {
            "ARN": "",
            "accountId": "111122223333", 
            "type": "AWS::KMS::Key" 
        }
    ],
    "eventType": "AwsApiCall", 
    "recipientAccountId": "111122223333"
}
```

**CreateGrant**  
When you use a [customer managed key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#customer-cmk) or an [AWS managed key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#aws-managed-cmk) to protect your DynamoDB table, DynamoDB uses [grants](#dynamodb-grants) to allow the service to perform continuous data protection and maintenance and durability tasks\. These grants are not required on [AWS owned key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#aws-owned-cmk)\.  
The grants that DynamoDB creates are specific to a table\. The principal in the [CreateGrant](https://docs.aws.amazon.com/kms/latest/APIReference/API_CreateGrant.html) request is the user who created the table\.   
The event that records the `CreateGrant` operation is similar to the following example event\. The parameters include the Amazon Resource Name \(ARN\) of the KMS key for the table, the grantee principal and retiring principal \( the DynamoDB service\), and the operations that the grant covers\. It also includes a constraint that requires all encryption operation use the specified [encryption context](#dynamodb-encryption-context)\.  

```
{ 
    "eventVersion": "1.05", 
    "userIdentity": 
    { 
        "type": "AssumedRole", 
        "principalId": "AROAIGDTESTANDEXAMPLE:user01", 
        "arn": "arn:aws:sts::111122223333:assumed-role/Admin/user01", 
        "accountId": "111122223333", 
        "accessKeyId": "AKIAIOSFODNN7EXAMPLE", 
        "sessionContext": { 
            "attributes": { 
                "mfaAuthenticated": "false", 
                "creationDate": "2018-02-14T00:12:02Z" 
            }, 
            "sessionIssuer": { 
                "type": "Role", 
                "principalId": "AROAIGDTESTANDEXAMPLE", 
                "arn": "arn:aws:iam::111122223333:role/Admin", 
                "accountId": "111122223333", 
                "userName": "Admin" 
            }
        }, 
        "invokedBy": "dynamodb.amazonaws.com" 
    }, 
    "eventTime": "2018-02-14T00:15:15Z", 
    "eventSource": "kms.amazonaws.com", 
    "eventName": "CreateGrant", 
    "awsRegion": "us-west-2", 
    "sourceIPAddress": "dynamodb.amazonaws.com", 
    "userAgent": "dynamodb.amazonaws.com", 
    "requestParameters": { 
        "keyId": "", 
        "retiringPrincipal": "dynamodb.us-west-2.amazonaws.com", 
        "constraints": { 
            "encryptionContextSubset": {
                "aws:dynamodb:tableName": "Books",
                "aws:dynamodb:subscriberId": "111122223333" 
            } 
        }, 
        "granteePrincipal": "dynamodb.us-west-2.amazonaws.com", 
        "operations": [ 
            "DescribeKey", 
            "GenerateDataKey", 
            "Decrypt", 
            "Encrypt", 
            "ReEncryptFrom", 
            "ReEncryptTo", 
            "RetireGrant" 
        ] 
    }, 
    "responseElements": { 
        "grantId": "5c5cd4a3d68e65e77795f5ccc2516dff057308172b0cd107c85b5215c6e48bde" 
    }, 
    "requestID": "2192b82a-111c-11e8-a528-f398979205d8", 
    "eventID": "a03d65c3-9fee-4111-9816-8bf96b73df01", 
    "readOnly": false, 
    "resources": [ 
        { 
            "ARN": "",
            "accountId": "111122223333", 
            "type": "AWS::KMS::Key" 
        } 
    ], 
    "eventType": "AwsApiCall",
    "recipientAccountId": "111122223333"
}
```