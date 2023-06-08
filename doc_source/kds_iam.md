# Using IAM policies for Amazon Kinesis Data Streams and Amazon DynamoDB<a name="kds_iam"></a>

The first time that you enable Amazon Kinesis Data Streams for Amazon DynamoDB, DynamoDB automatically creates an AWS Identity and Access Management \(IAM\) service\-linked role for you\. This role, `AWSServiceRoleForDynamoDBKinesisDataStreamsReplication`, allows DynamoDB to manage the replication of item\-level changes to Kinesis Data Streams on your behalf\. Don't delete this service\-linked role\.

For more information about service\-linked roles, see [Using service\-linked roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html) in the *IAM User Guide*\.

To enable Amazon Kinesis Data Streams for Amazon DynamoDB, you must have the following permissions on the table:
+ `dynamodb:EnableKinesisStreamingDestination`
+ `kinesis:ListStreams`
+ `kinesis:PutRecords`

To describe Amazon Kinesis Data Streams for Amazon DynamoDB for a given DynamoDB table, you must have the following permissions on the table\.
+ `dynamodb:DescribeKinesisStreamingDestination`
+ `kinesis:DescribeStreamSummary`
+ `kinesis:DescribeStream`

To disable Amazon Kinesis Data Streams for Amazon DynamoDB, you must have the following permissions on the table\.
+ `dynamodb:DisableKinesisStreamingDestination`

The following examples show how to use IAM policies to grant permissions for Amazon Kinesis Data Streams for Amazon DynamoDB\.

## Example: Enable Amazon Kinesis Data Streams for Amazon DynamoDB<a name="access-policy-kds-example1"></a>

The following IAM policy grants permissions to enable Amazon Kinesis Data Streams for Amazon DynamoDB for the `Music` table and does not grant permissions to disable or describe Kinesis Data Streams for DynamoDB for the `Music` table\. 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "iam:CreateServiceLinkedRole",
            "Resource": "arn:aws:iam::*:role/aws-service-role/kinesisreplication.dynamodb.amazonaws.com/AWSServiceRoleForDynamoDBKinesisDataStreamsReplication",
            "Condition": {"StringLike": {"iam:AWSServiceName": "kinesisreplication.dynamodb.amazonaws.com"}}
        },
        {
            "Effect": "Allow",
            "Action": [

                "dynamodb:EnableKinesisStreamingDestination"

            ],
            "Resource": "arn:aws:dynamodb:us-west-2:123456789012:table/Music"
        }
    ]
}
```

## Example: Disable Amazon Kinesis Data Streams for Amazon DynamoDB<a name="access-policy-kds-example2"></a>

The following IAM policy grants permissions to disable Amazon Kinesis Data Streams for Amazon DynamoDB for the `Music` table and does not grant permissions to enable or describe Amazon Kinesis Data Streams for Amazon DynamoDB for the `Music` table\. 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [                
                "dynamodb:DisableKinesisStreamingDestination"
            ],
            "Resource": "arn:aws:dynamodb:us-west-2:123456789012:table/Music"
        }
    ]
}
```

## Example: Selectively apply permissions for Amazon Kinesis Data Streams for Amazon DynamoDB based on resource<a name="access-policy-kds-example3"></a>

The following IAM policy grants permissions to enable and describe Amazon Kinesis Data Streams for Amazon DynamoDB for the `Music` table and denies permissions to disable Amazon Kinesis Data Streams for Amazon DynamoDB for the `Orders` table\. 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "dynamodb:EnableKinesisStreamingDestination",
                "dynamodb:DescribeKinesisStreamingDestination"
            ],
            "Resource": "arn:aws:dynamodb:us-west-2:123456789012:table/Music"
        },
        {
            "Effect": "Deny",
            "Action": [
                "dynamodb:DisableKinesisStreamingDestination"
            ],
            "Resource": "arn:aws:dynamodb:us-west-2:123456789012:table/Orders"
        }
        
    ]
}
```

## Using service\-linked roles for Kinesis Data Streams for DynamoDB<a name="kds-service-linked-roles"></a>

Amazon Kinesis Data Streams for Amazon DynamoDB uses AWS Identity and Access Management \(IAM\)[ service\-linked roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html#iam-term-service-linked-role)\. A service\-linked role is a unique type of IAM role that is linked directly to Kinesis Data Streams for DynamoDB\. Service\-linked roles are predefined by Kinesis Data Streams for DynamoDB and include all the permissions that the service requires to call other AWS services on your behalf\. 

A service\-linked role makes setting up Kinesis Data Streams for DynamoDB easier because you don’t have to manually add the necessary permissions\. Kinesis Data Streams for DynamoDB defines the permissions of its service\-linked roles, and unless defined otherwise, only Kinesis Data Streams for DynamoDB can assume its roles\. The defined permissions include the trust policy and the permissions policy, and that permissions policy cannot be attached to any other IAM entity\.

For information about other services that support service\-linked roles, see [AWS Services That Work with IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_aws-services-that-work-with-iam.html) and look for the services that have **Yes **in the **Service\-Linked Role** column\. Choose a **Yes** with a link to view the service\-linked role documentation for that service\.

### Service\-linked role permissions for Kinesis Data Streams for DynamoDB<a name="slr-permissions"></a>

Kinesis Data Streams for DynamoDB uses the service\-linked role named **AWSServiceRoleForDynamoDBKinesisDataStreamsReplication**\. The purpose of the service\-linked role is to allow Amazon DynamoDB to manage the replication of item\-level changes to Kinesis Data Streams, on your behalf\.

The `AWSServiceRoleForDynamoDBKinesisDataStreamsReplication` service\-linked role trusts the following services to assume the role:
+ `kinesisreplication.dynamodb.amazonaws.com`

The role permissions policy allows Kinesis Data Streams for DynamoDB to complete the following actions on the specified resources:
+ Action: `Put records and describe` on `Kinesis stream`
+ Action: `Generate data keys` on `AWS KMS` in order to put data on Kinesis streams that are encrypted using User\-Generated AWS KMS keys\.

For the exact contents of the policy document, see [DynamoDBKinesisReplicationServiceRolePolicy](https://console.aws.amazon.com/iam/home#policies/arn:aws:iam::aws:policy/aws-service-role/DynamoDBKinesisReplicationServiceRolePolicy)\.

You must configure permissions to allow an IAM entity \(such as a user, group, or role\) to create, edit, or delete a service\-linked role\. For more information, see [Service\-Linked Role Permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/contributorinsights-service-linked-roles.html#service-linked-role-permissions) in the *IAM User Guide*\.

### Creating a service\-linked role for Kinesis Data Streams for DynamoDB<a name="create-slr"></a>

You don't need to manually create a service\-linked role\. When you enable Kinesis Data Streams for DynamoDB in the AWS Management Console, the AWS CLI, or the AWS API, Kinesis Data Streams for DynamoDB creates the service\-linked role for you\. 

If you delete this service\-linked role, and then need to create it again, you can use the same process to recreate the role in your account\. When you enable Kinesis Data Streams for DynamoDB, Kinesis Data Streams for DynamoDB creates the service\-linked role for you again\. 

### Editing a service\-linked role for Kinesis Data Streams for DynamoDB<a name="edit-slr"></a>

Kinesis Data Streams for DynamoDB does not allow you to edit the `AWSServiceRoleForDynamoDBKinesisDataStreamsReplication` service\-linked role\. After you create a service\-linked role, you cannot change the name of the role because various entities might reference the role\. However, you can edit the description of the role using IAM\. For more information, see [Editing a Service\-Linked Role](https://docs.aws.amazon.com/IAM/latest/UserGuide/contributorinsights-service-linked-roles.html#edit-service-linked-role) in the *IAM User Guide*\.

### Deleting a service\-linked role for Kinesis Data Streams for DynamoDB<a name="delete-slr"></a>

You can also use the IAM console, the AWS CLI or the AWS API to manually delete the service\-linked role\. To do this, you must first manually clean up the resources for your service\-linked role and then you can manually delete it\.

**Note**  
If the Kinesis Data Streams for DynamoDB service is using the role when you try to delete the resources, then the deletion might fail\. If that happens, wait for a few minutes and try the operation again\.

**To manually delete the service\-linked role using IAM**

Use the IAM console, the AWS CLI, or the AWS API to delete the `AWSServiceRoleForDynamoDBKinesisDataStreamsReplication` service\-linked role\. For more information, see [Deleting a Service\-Linked Role](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html) in the *IAM User Guide*\.