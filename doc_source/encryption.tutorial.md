# Managing Encrypted Tables<a name="encryption.tutorial"></a>

This section describes how to use the Amazon DynamoDB console and the AWS Command Line Interface \(AWS CLI\) to manage encrypted tables\. 

**Topics**
+ [Creating an Encrypted Table \(Console\)](#encryption.tutorial-console)
+ [Creating an Encrypted Table \(AWS CLI\)](#encryption.tutorial-cli)
+ [Updating Encryption Key \(Console\)](#encryption.tutorial-update-console)
+ [Updating Encryption Key \(AWS CLI\)](#encryption.tutorial-update-cli)

## Creating an Encrypted Table \(Console\)<a name="encryption.tutorial-console"></a>

Follow these steps to create an encrypted table using the Amazon DynamoDB console\.

1. Sign in to the AWS Management Console and open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1.  In the navigation pane on the left side of the console, choose **Tables**\.

1.  Choose **Create Table**\. For the **Table name**, enter **Music**\. For the primary key, use **Artist**, and for the sort key, use **SongTitle**, both as strings\. In **Table settings**, make sure that **Use default settings** is not selected\.  
![\[Screenshot of table settings in the console showing the use default settings check box.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/tablesettings.PNG)
**Note**  
 If **Use default settings** is selected, tables are encrypted at rest with the AWS owned customer master key \(CMK\) at no additional cost\. 

1. Under **Encryption at rest**, choose an encryption type:   
![\[Screenshot of encryption settings in the console showing encryption types check box.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/encryption_types.PNG)
   +  Default – AWS owned CMK\. Key is owned by DynamoDB \(no additional charge\)\. 
   +  KMS – AWS managed CMK\. Key is stored in your account and is managed by AWS KMS \(AWS KMS charges apply\)\. 

    Then choose **Create** to create the encrypted table\. To confirm the encryption type, check the table details under the **Overview** tab\. 

## Creating an Encrypted Table \(AWS CLI\)<a name="encryption.tutorial-cli"></a>

Follow these steps to create an encrypted table with the default AWS owned CMK using the AWS CLI\.
+ Create the encrypted `Music` table:

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

Follow these steps to create an encrypted table with the AWS managed CMK for DynamoDB \(`aws/dynamodb`\) using the AWS CLI\.
+ Create the encrypted `Music` table:

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

   The `SSEDescription` status of the table description is set to `ENABLED` and the `SSEType` is `KMS`: 

  ```
   "SSEDescription": {
    "SSEType": "KMS",
    "Status": "ENABLED",
    "KMSMasterKeyArn": "arn:aws:kms:us-east-1:123456789012:key/abcd1234-abcd-1234-a123-ab1234a1b234"
    },
  }
  ```

## Updating Encryption Key \(Console\)<a name="encryption.tutorial-update-console"></a>

Follow these steps to update an encrypted table from an AWS owned CMK to an AWS managed CMK or the other way around\.

1. Sign in to the AWS Management Console and open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1.  In the navigation pane on the left side of the console, choose **Tables**\.

1.  Choose the Table you wish to update and select the **Overview** tab\. Choose on the **Manage Encryption** link\.  
![\[Screenshot of table settings in the console showing the default encryption.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/encryption-manage.png)

1. Choose an encryption type:   
![\[Screenshot of encryption settings in the console showing encryption types check box.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/encryption-change.PNG)
   +  Default – AWS owned CMK\. Key is owned by DynamoDB \(no additional charge\)\. 
   +  KMS – AWS managed CMK\. Key is stored in your account and is managed by AWS KMS \(AWS KMS charges apply\)\. 

    Then choose **Save** to update the encrypted table\. To confirm the encryption type, check the table details under the **Overview** tab\. 

## Updating Encryption Key \(AWS CLI\)<a name="encryption.tutorial-update-cli"></a>

Follow these steps to update an encrypted table with the default AWS owned CMK using the AWS CLI\.
+ Update the encrypted `Music` table:

  ```
  aws dynamodb update-table \
    --table-name Music \
    --sse-specification Enabled=false
  ```
**Note**  
 This table is now encrypted using the default AWS owned CMK in the DynamoDB service account\. 

Follow these steps to update an encrypted table with the AWS managed CMK for DynamoDB `(aws/dynamodb)` using the AWS CLI\.
+ Update the encrypted `Music` table:

  ```
  aws dynamodb update-table \
    --table-name Music \
    --sse-specification Enabled=true
  ```

   The `SSEDescription` status of the table description is set to `ENABLED` and the `SSEType` is `KMS`: 

  ```
   "SSEDescription": {
    "SSEType": "KMS",
    "Status": "ENABLED",
    "KMSMasterKeyArn": "arn:aws:kms:us-east-1:123456789012:key/abcd1234-abcd-1234-a123-ab1234a1b234"
    },
  }
  ```