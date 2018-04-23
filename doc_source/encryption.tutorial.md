# Enabling Encryption at Rest<a name="encryption.tutorial"></a>

This section describes how to use the Amazon DynamoDB console, the AWS Command Line Interface \(AWS CLI\), or the DynamoDB API to encrypt a table\. 

**Topics**
+ [Enabling Encryption at Rest \(Console\)](#encryption.tutorial-console)
+ [Enabling Encryption at Rest \(AWS CLI\)](#encryption.tutorial-cli)

## Enabling Encryption at Rest \(Console\)<a name="encryption.tutorial-console"></a>

Follow these steps to enable encryption at rest on a table using the Amazon DynamoDB console\.

1. Sign in to the AWS Management Console and open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1.  In the navigation pane on the left side of the console, choose **Tables**\.

1.  Choose **Create Table**\. For the **Table name**, type **Music**\. For the primary key, use **Artist**, and for the sort key, use **SongTitle**, both as strings\. In **Table settings**, make sure that **Use default settings** is not selected\.  
![\[Screenshot of table settings in the console showing the use default settings check box.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/tablesettings.PNG)

1. Under **Encryption at rest**, choose **Enable encryption**\. Then choose **Create** to create the encrypted table\.
**Important**  
Encryption at rest can be enabled only when you are creating a new DynamoDB table\. Currently, you can't enable encryption at rest on an existing table\. Once encryption at rest is enabled, it can't be disabled\. We recommend that you enable encryption for any tables that contain sensitive data\.

To confirm that the table is encrypted, check the table details under the **Overview** tab\. Encryption should be **ENABLED**\.

## Enabling Encryption at Rest \(AWS CLI\)<a name="encryption.tutorial-cli"></a>

Follow these steps to create an encrypted table `Music` using the AWS CLI\.
+ Enable encryption on the `Music` table:

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
    --sse-specification Enabled=true
  ```

   The `SSEDescription` status of the table description is set to `ENABLED`: 

  ```
  {
    "SSEDescription": {
        "Status": "ENABLED"
    }
  }
  ```