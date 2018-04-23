# Amazon DynamoDB Encryption at Rest<a name="EncryptionAtRest"></a>

Amazon DynamoDB offers fully managed encryption at rest\. DynamoDB encryption at rest provides enhanced security by encrypting your data at rest using an AWS Key Management Service \(AWS KMS\) managed encryption key for DynamoDB\. This functionality eliminates the operational burden and complexity involved in protecting sensitive data\.

To get started with encryption at rest, you need to create a table with encryption at rest enabled\.

**Important**  
Encryption at rest can be enabled only when you are creating a new DynamoDB table\. Currently, you can't enable encryption at rest on an existing table\. After encryption at rest is enabled, it can't be disabled\. We recommend that you enable encryption for any tables that contain sensitive data\.

For more information, see [Encryption at Rest: How It Works](encryption.howitworks.md)\. You can enable encryption at rest using the AWS Management Console, AWS Command Line Interface \(AWS CLI\), or the Amazon DynamoDB API\. For more information, see [Enabling Encryption at Rest](encryption.tutorial.md)\.

**Note**  
Currently, encryption at rest is supported in the following AWS Regions:  
US East \(Ohio\)
US East \(N\. Virginia\)
US West \(Oregon\)
US West \(N\. California\)
Canada \(Central\)
South America \(São Paulo\)
EU \(Ireland\)
EU \(London\)
EU \(Frankfurt\)
Asia Pacific \(Mumbai\)
Asia Pacific \(Tokyo\)
Asia Pacific \(Seoul\)
Asia Pacific \(Singapore\)
Asia Pacific \(Sydney\)

 Encryption at rest is offered at no additional cost \(KMS encryption key usage charges apply\)\. For information on pricing, see [Amazon DynamoDB Pricing](https://aws.amazon.com/dynamodb/pricing)\. 