# Managing Encrypted Tables in DynamoDB<a name="encryption.tutorial"></a>

You can use the AWS Management Console or the AWS Command Line Interface \(AWS CLI\) to specify the encryption key on new tables and update the encryption keys on existing tables in Amazon DynamoDB\.

**Topics**
+ [Specifying the Encryption Key for a New Table](#encryption.tutorial-creating)
+ [Updating an Encryption Key](#encryption.tutorial-update)

## Specifying the Encryption Key for a New Table<a name="encryption.tutorial-creating"></a>

Follow these steps to specify the encryption key on a new table using the Amazon DynamoDB console or the AWS CLI\.

### Creating an Encrypted Table \(Console\)<a name="encryption.tutorial-console"></a>

1. Sign in to the AWS Management Console and open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1.  In the navigation pane on the left side of the console, choose **Tables**\.

1. Choose **Create Table**\. For the **Table name**, enter **Music**\. For the primary key, enter **Artist**, and for the sort key, enter **SongTitle**, both as strings\.

1. In **Table settings**, make sure that **Use default settings** is not selected\.  
![\[Screenshot of table settings on the console showing the use default settings check box.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/tablesettings.PNG)
**Note**  
If **Use default settings** is selected, tables are encrypted at rest with the AWS owned customer master key \(CMK\) at no additional cost\.

1. Under **Encryption at rest**, choose an encryption type:
   +  **Default** – AWS owned CMK\. The key is owned by DynamoDB \(no additional charge\)\.
   + **KMS** – Customer managed CMK\. The key is stored in your account and is created, owned, and managed by you \(AWS KMS charges apply\)\.
   +  **KMS** – AWS managed CMK\. The key is stored in your account and is managed by AWS Key Management Service \(AWS KMS charges apply\)\.  
![\[Console screenshot of encryption settings on the console showing 3 encryption types radio buttons.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/encryption_types.PNG)![\[Console screenshot of encryption settings on the console showing 3 encryption types radio buttons.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Console screenshot of encryption settings on the console showing 3 encryption types radio buttons.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

1. Choose **Create** to create the encrypted table\. To confirm the encryption type, check the table details on the **Overview** tab\. 

### Creating an Encrypted Table \(AWS CLI\)<a name="encryption.tutorial-cli"></a>

Use the AWS CLI to create a table with the default AWS owned CMK, the AWS managed CMK, or a customer managed CMK for Amazon DynamoDB\.

**To create an encrypted table with the default AWS owned CMK**
+ Create the encrypted `Music` table as follows\.

  ```
  aws dynamodb create-table \
    --table-name Music \
    --attribute-definitions \
        AttributeName=Artist,AttributeType=S \
        AttributeName=SongTitle,AttributeType=S \
    --key-schema \
        AttributeName=Artist,KeyType=HASH \
        AttributeName=SongTitle,KeyType=RANGE \
    --provisioned-throughput \
        ReadCapacityUnits=10,WriteCapacityUnits=5
  ```
**Note**  
 This table is now encrypted using the default AWS owned CMK in the DynamoDB service account\. 

**To create an encrypted table with the AWS managed CMK for DynamoDB**
+ Create the encrypted `Music` table as follows\.

  ```
  aws dynamodb create-table \
    --table-name Music \
    --attribute-definitions \
        AttributeName=Artist,AttributeType=S \
        AttributeName=SongTitle,AttributeType=S \
    --key-schema \
        AttributeName=Artist,KeyType=HASH \
        AttributeName=SongTitle,KeyType=RANGE \
    --provisioned-throughput \
        ReadCapacityUnits=10,WriteCapacityUnits=5 \
    --sse-specification Enabled=true,SSEType=KMS
  ```

   The `SSEDescription` status of the table description is set to `ENABLED` and the `SSEType` is `KMS`\. 

  ```
  "SSEDescription": {
    "SSEType": "KMS",
    "Status": "ENABLED",
    "KMSMasterKeyArn": "arn:aws:kms:us-east-1:123456789012:key/abcd1234-abcd-1234-a123-ab1234a1b234",
  }
  ```

**To create an encrypted table with a customer managed CMK for DynamoDB**
+ Create the encrypted `Music` table as follows\.

  ```
  aws dynamodb create-table \
    --table-name Music \
    --attribute-definitions \
        AttributeName=Artist,AttributeType=S \
        AttributeName=SongTitle,AttributeType=S \
    --key-schema \
        AttributeName=Artist,KeyType=HASH \
        AttributeName=SongTitle,KeyType=RANGE \
    --provisioned-throughput \
        ReadCapacityUnits=10,WriteCapacityUnits=5 \
    --sse-specification Enabled=true,SSEType=KMS,KMSMasterKeyId=abcd1234-abcd-1234-a123-ab1234a1b234
  ```

   The `SSEDescription` status of the table description is set to `ENABLED` and the `SSEType` is `KMS`\.

  ```
  "SSEDescription": {
    "SSEType": "KMS",
    "Status": "ENABLED",
    "KMSMasterKeyArn": "arn:aws:kms:us-east-1:123456789012:key/abcd1234-abcd-1234-a123-ab1234a1b234",
  }
  ```

## Updating an Encryption Key<a name="encryption.tutorial-update"></a>

You can also use the DynamoDB console or the AWS CLI to update the encryption keys of an existing table between an AWS owned CMK, AWS managed CMK, and customer managed CMK at any time\.

### Updating an Encryption Key \(Console\)<a name="encryption.tutorial-update-console"></a>

1. Sign in to the AWS Management Console and open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1.  In the navigation pane on the left side of the console, choose **Tables**\.

1. Choose the table that you want to update, and then choose the **Overview** tab\.

1. Choose **Manage Encryption**\.  
![\[Screenshot of table settings in the console showing the default encryption.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/encryption-manage.png)![\[Screenshot of table settings in the console showing the default encryption.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Screenshot of table settings in the console showing the default encryption.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

1. Choose an encryption type\.   
![\[Screenshot of encryption settings on the console showing encryption types radio buttons.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/encryption-change.PNG)![\[Screenshot of encryption settings on the console showing encryption types radio buttons.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Screenshot of encryption settings on the console showing encryption types radio buttons.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)
   +  **Default** – AWS owned CMK\. The key is owned by DynamoDB \(no additional charge\)\. 
   + **KMS** – Customer managed CMK\. The key is stored in your account and is created, owned, and managed by you \(AWS KMS charges apply\)\.
   +  **KMS** – AWS managed CMK\. The key is stored in your account and is managed by AWS Key Management Service \(AWS KMS charges apply\)\. 

   Then choose **Save** to update the encrypted table\. To confirm the encryption type, check the table details under the **Overview** tab\. 

### Updating an Encryption Key \(AWS CLI\)<a name="encryption.tutorial-update-cli"></a>

The following examples show how to update an encrypted table using the AWS CLI\.

**To update an encrypted table with the default AWS owned CMK**
+ Update the encrypted `Music` table, as in the following example\.

  ```
  aws dynamodb update-table \
    --table-name Music \
    --sse-specification Enabled=false
  ```
**Note**  
This table is now encrypted using the default AWS owned CMK in the DynamoDB service account\. 

**To update an encrypted table with the AWS managed CMK for DynamoDB**
+ Update the encrypted `Music` table, as in the following example\.

  ```
  aws dynamodb update-table \
    --table-name Music \
    --sse-specification Enabled=true
  ```

   The `SSEDescription` status of the table description is set to `ENABLED` and the `SSEType` is `KMS`\.

  ```
  "SSEDescription": {
    "SSEType": "KMS",
    "Status": "ENABLED",
    "KMSMasterKeyArn": "arn:aws:kms:us-east-1:123456789012:key/abcd1234-abcd-1234-a123-ab1234a1b234",
  }
  ```

**To update an encrypted table with a customer managed CMK for DynamoDB**
+ Update the encrypted `Music` table, as in the following example\.

  ```
  aws dynamodb update-table \
    --table-name Music \
    --sse-specification Enabled=true,SSEType=KMS,KMSMasterKeyId=abcd1234-abcd-1234-a123-ab1234a1b234
  ```

   The `SSEDescription` status of the table description is set to `ENABLED` and the `SSEType` is `KMS`\. 

  ```
  "SSEDescription": {
    "SSEType": "KMS",
    "Status": "ENABLED",
    "KMSMasterKeyArn": "arn:aws:kms:us-east-1:123456789012:key/abcd1234-abcd-1234-a123-ab1234a1b234",
  }
  ```