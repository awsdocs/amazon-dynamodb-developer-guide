# Managing encrypted tables in DynamoDB<a name="encryption.tutorial"></a>

You can use the AWS Management Console or the AWS Command Line Interface \(AWS CLI\) to specify the encryption key on new tables and update the encryption keys on existing tables in Amazon DynamoDB\.

**Topics**
+ [Specifying the encryption key for a new table](#encryption.tutorial-creating)
+ [Updating an encryption key](#encryption.tutorial-update)

## Specifying the encryption key for a new table<a name="encryption.tutorial-creating"></a>

Follow these steps to specify the encryption key on a new table using the Amazon DynamoDB console or the AWS CLI\.

### Creating an encrypted table \(console\)<a name="encryption.tutorial-console"></a>

1. Sign in to the AWS Management Console and open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1.  In the navigation pane on the left side of the console, choose **Tables**\.

1. Choose **Create Table**\. For the **Table name**, enter **Music**\. For the primary key, enter **Artist**, and for the sort key, enter **SongTitle**, both as strings\.

1. In **Settings**, make sure that **Customize settings** is selected\.
**Note**  
If **Use default settings** is selected, tables are encrypted at rest with the AWS owned key at no additional cost\.

1. Under **Encryption at rest**, choose an encryption type \- AWS owned key, AWS managed key, or customer managed key\.
   +  **Owned by Amazon DynamoDB\.** AWS owned key, specifically owned and managed by DynamoDB\. You are not charged an additional fee for using this key\.
   + **AWS managed key**\. Key alias: `aws/dynamodb`\. The key is stored in your account and is managed by AWS Key Management Service \(AWS KMS\)\. AWS KMS charges apply\.
   +  **Stored in your account, and owned and managed by you\.** Customer managed key\. The key is stored in your account and is managed by AWS Key Management Service \(AWS KMS\)\. AWS KMS charges apply\.
**Note**  
If you select to own and manage your own key, make sure the KMS Key Policy is appropriately set\. For more information including examples, see [ Key policy for a customer managed key](https://docs.aws.amazon.com/kms/latest/developerguide/services-dynamodb.html#dynamodb-customer-cmk-policy)\.

1. Choose **Create table** to create the encrypted table\. To confirm the encryption type, select the table details on the **Overview** tab and review the **Additional details** section\.

### Creating an encrypted table \(AWS CLI\)<a name="encryption.tutorial-cli"></a>

Use the AWS CLI to create a table with the default AWS owned key, the AWS managed key, or a customer managed key for Amazon DynamoDB\.

**To create an encrypted table with the default AWS owned key**
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
This table is now encrypted using the default AWS owned key in the DynamoDB service account\.

**To create an encrypted table with the AWS managed key for DynamoDB**
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

**To create an encrypted table with a customer managed key for DynamoDB**
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

## Updating an encryption key<a name="encryption.tutorial-update"></a>

You can also use the DynamoDB console or the AWS CLI to update the encryption keys of an existing table between an AWS owned key, AWS managed key, and customer managed key at any time\.

### Updating an encryption key \(console\)<a name="encryption.tutorial-update-console"></a>

1. Sign in to the AWS Management Console and open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1.  In the navigation pane on the left side of the console, choose **Tables**\.

1. Choose the table that you want to update\.

1. Select the **Actions** dropdown, and then select the **Update settings** option\.

1. Go to the **Additional settings** tab\.

1. Under **Encryption**, choose **Manage encryption**\.

1. Choose an encryption type:
   +  **Owned by Amazon DynamoDB\.** The AWS KMS key is owned and managed by DynamoDB\. You are not charged an additional fee for using this key\.
   + **AWS managed key** Key alias: `aws/dynamodb`\. The key is stored in your account and is managed by AWS Key Management Service\. \(AWS KMS\)\. AWS KMS charges apply\.
   +  **Stored in your account, and owned and managed by you\.** The key is stored in your account and is managed by AWS Key Management Service\. \(AWS KMS\)\. AWS KMS charges apply\.
**Note**  
If you select to own and manage your own key, make sure the KMS Key Policy is appropriately set\. For more information see [ Key policy for a customer managed key](https://docs.aws.amazon.com/kms/latest/developerguide/services-dynamodb.html#dynamodb-customer-cmk-policy)\.

   Then choose **Save** to update the encrypted table\. To confirm the encryption type, check the table details under the **Overview** tab\.

### Updating an encryption key \(AWS CLI\)<a name="encryption.tutorial-update-cli"></a>

The following examples show how to update an encrypted table using the AWS CLI\.

**To update an encrypted table with the default AWS owned key**
+ Update the encrypted `Music` table, as in the following example\.

  ```
  aws dynamodb update-table \
    --table-name Music \
    --sse-specification Enabled=false
  ```
**Note**  
This table is now encrypted using the default AWS owned key in the DynamoDB service account\.

**To update an encrypted table with the AWS managed key for DynamoDB**
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

**To update an encrypted table with a customer managed key for DynamoDB**
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