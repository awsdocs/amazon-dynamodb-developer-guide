# Logging DynamoDB Operations by Using AWS CloudTrail<a name="logging-using-cloudtrail"></a>

DynamoDB is integrated with CloudTrail, a service that captures low\-level API requests made by or on behalf of DynamoDB in your AWS account and delivers the log files to an Amazon S3 bucket that you specify\. CloudTrail captures calls made from the DynamoDB console or from the DynamoDB low\-level API\. Using the information collected by CloudTrail, you can determine what request was made to DynamoDB, the source IP address from which the request was made, who made the request, when it was made, and so on\. To learn more about CloudTrail, including how to configure and enable it, see the [http://docs.aws.amazon.com/awscloudtrail/latest/userguide/](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/)\.

## DynamoDB Information in CloudTrail<a name="service-name-info-in-cloudtrail"></a>

When CloudTrail logging is enabled in your AWS account, low\-level API calls made to DynamoDB actions are tracked in log files\. DynamoDB records are written together with other AWS service records in a log file\. CloudTrail determines when to create and write to a new file based on a time period and file size\.

The following API actions are supported:

** Amazon DynamoDB **

+ [CreateBackup](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_CreateBackup.html)

+ [CreateGlobalTable](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_CreateGlobalTable.html)

+ [CreateTable](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_CreateTable.html)

+ [DeleteBackup](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DeleteBackup.html)

+ [DeleteTable](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DeleteTable.html)

+ [DescribeBackup](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DescribeBackup.html)

+ [DescribeContinuousBackups](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DescribeContinuousBackups.html)

+ [DescribeGlobalTable](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DescribeGlobalTable.html)

+ [DescribeLimits](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DescribeLimits.html)

+ [DescribeTable](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DescribeTable.html)

+ [DescribeTimeToLive](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DescribeTimeToLive.html)

+ [ListBackups](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_ListBackups.html)

+ [ListTables](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_ListTables.html)

+ [ListTagsOfResource](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_ListTagsOfResource.html)

+ [ListGlobalTables](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_ListGlobalTables.html)

+ [RestoreTableFromBackup](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_RestoreTableFromBackup.html)

+ [TagResource](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_TagResource.html)

+ [UntagResource](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_UntagResource.html)

+ [UpdateGlobalTable](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_UpdateGlobalTable.html)

+ [UpdateTable](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_UpdateTable.html)

+ [UpdateTimeToLive](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_UpdateTimeToLive.html)

+ DescribeReservedCapacity

+ DescribeReservedCapacityOfferings

+ PurchaseReservedCapacityOfferings

** DynamoDB Streams **

+ [DescribeStream](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_streams_DescribeStream.html)

+ [ListStreams](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_streams_ListStreams.html)

** DynamoDB Accelerator \(DAX\) **

+ [CreateCluster](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_CreateCluster.html)

+ [CreateParameterGroup](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_CreateParameterGroup.html)

+ [CreateSubnetGroup](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_CreateSubnetGroup.html)

+ [DecreaseReplicationFactor](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_DecreaseReplicationFactor.html)

+ [DeleteCluster](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_DeleteCluster.html)

+ [DeleteParameterGroup](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_DeleteParameterGroup.html)

+ [DeleteSubnetGroup](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_DeleteSubnetGroup.html)

+ [DescribeClusters](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_DescribeClusters.html)

+ [DescribeDefaultParameters](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_DescribeDefaultParameters.html)

+ [DescribeEvents](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_DescribeEvents.html)

+ [DescribeParameterGroups](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_DescribeParameterGroups.html)

+ [DescribeParameters](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_DescribeParameters.html)

+ [DescribeSubnetGroups](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_DescribeSubnetGroups.html)

+ [IncreaseReplicationFactor](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_IncreaseReplicationFactor.html)

+ [ListTags](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_ListTags.html)

+ [RebootNode](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_RebootNode.html)

+ [TagResource](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_TagResource.html)

+ [UntagResource](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_UntagResource.html)

+ [UpdateCluster](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_UpdateCluster.html)

+ [UpdateParameterGroup](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_UpdateParameterGroup.html)

+ [UpdateSubnetGroup](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_UpdateSubnetGroup.html)

Every log entry contains information about who generated the request\. The user identity information in the log helps you determine whether the request was made with root or IAM user credentials, with temporary security credentials for a role or federated user, or by another AWS service\. For more information, see the **userIdentity** field in the [CloudTrail Event Reference](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference.html)\.

You can store your log files in your bucket for as long as you want, but you can also define Amazon S3 lifecycle rules to archive or delete log files automatically\. By default, your log files are encrypted by using Amazon S3 server\-side encryption \(SSE\)\.

You can choose to have CloudTrail publish Amazon SNS notifications when new log files are delivered if you want to take quick action upon log file delivery\. For more information, see [Configuring Amazon SNS Notifications](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/configure-sns-notifications-for-cloudtrail.html)\.

You can also aggregate DynamoDB log files from multiple AWS regions and multiple AWS accounts into a single Amazon S3 bucket\. For more information, see [Receiving CloudTrail Log Files from Multiple Regions ](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/receive-cloudtrail-log-files-from-multiple-regions.html)\.

## Understanding DynamoDB Log File Entries<a name="understanding-service-name-entries"></a>

CloudTrail log files contain one or more log entries where each entry is made up of multiple JSON\-formatted events\. A log entry represents a single request from any source and includes information about the requested action, any parameters, the date and time of the action, and so on\. The log entries are not guaranteed to be in any particular order\. That is, they are not an ordered stack trace of the low\-level DynamoDB API calls\.

The following example shows a CloudTrail log\.

```
{"Records": [
    {
        "eventVersion": "1.03",
        "userIdentity": {
            "type": "AssumedRole",
            "principalId": "AKIAIOSFODNN7EXAMPLE:bob",
            "arn": "arn:aws:sts::111122223333:assumed-role/users/bob",
            "accountId": "111122223333",
            "accessKeyId": "AKIAIOSFODNN7EXAMPLE",
            "sessionContext": {
                "attributes": {
                    "mfaAuthenticated": "false",
                    "creationDate": "2015-05-28T18:06:01Z"
                },
                "sessionIssuer": {
                    "type": "Role",
                    "principalId": "AKIAI44QH8DHBEXAMPLE",
                    "arn": "arn:aws:iam::444455556666:role/admin-role",
                    "accountId": "444455556666",
                    "userName": "bob"
                }
            }
        },
        "eventTime": "2015-05-01T07:24:55Z",
        "eventSource": "dynamodb.amazonaws.com",
        "eventName": "CreateTable",
        "awsRegion": "us-west-2",
        "sourceIPAddress": "192.0.2.0",
        "userAgent": "console.aws.amazon.com",
        "requestParameters": {
            "provisionedThroughput": {
                "writeCapacityUnits": 10,
                "readCapacityUnits": 10
            },
            "tableName": "Music",
            "keySchema": [
                {
                    "attributeName": "Artist",
                    "keyType": "HASH"
                },
                {
                    "attributeName": "SongTitle",
                    "keyType": "RANGE"
                }
            ],
            "attributeDefinitions": [
                {
                    "attributeType": "S",
                    "attributeName": "Artist"
                },
                {
                    "attributeType": "S",
                    "attributeName": "SongTitle"
                }
            ]
        },
        "responseElements": {"tableDescription": {
            "tableName": "Music",
            "attributeDefinitions": [
                {
                    "attributeType": "S",
                    "attributeName": "Artist"
                },
                {
                    "attributeType": "S",
                    "attributeName": "SongTitle"
                }
            ],
            "itemCount": 0,
            "provisionedThroughput": {
                "writeCapacityUnits": 10,
                "numberOfDecreasesToday": 0,
                "readCapacityUnits": 10
            },
            "creationDateTime": "May 1, 2015 7:24:55 AM",
            "keySchema": [
                {
                    "attributeName": "Artist",
                    "keyType": "HASH"
                },
                {
                    "attributeName": "SongTitle",
                    "keyType": "RANGE"
                }
            ],
            "tableStatus": "CREATING",
            "tableSizeBytes": 0
        }},
        "requestID": "KAVGJR1Q0I5VHF8FS8V809EV7FVV4KQNSO5AEMVJF66Q9ASUAAJG",
        "eventID": "a8b5f864-480b-43bf-bc22-9b6d77910a29",
        "eventType": "AwsApiCall",
        "apiVersion": "2012-08-10",
        "recipientAccountId": "111122223333"
    },

    {
        "eventVersion": "1.03",
        "userIdentity": {
            "type": "AssumedRole",
            "principalId": "AKIAIOSFODNN7EXAMPLE:bob",
            "arn": "arn:aws:sts::111122223333:assumed-role/users/bob",
            "accountId": "444455556666",
            "accessKeyId": "AKIAIOSFODNN7EXAMPLE",
            "sessionContext": {
                "attributes": {
                    "mfaAuthenticated": "false",
                    "creationDate": "2015-05-28T18:06:01Z"
                },
                "sessionIssuer": {
                    "type": "Role",
                    "principalId": "AKIAI44QH8DHBEXAMPLE",
                    "arn": "arn:aws:iam::444455556666:role/admin-role",
                    "accountId": "444455556666",
                    "userName": "bob"
                }
            }
        },
        "eventTime": "2015-05-04T02:43:11Z",
        "eventSource": "dynamodb.amazonaws.com",
        "eventName": "DescribeTable",
        "awsRegion": "us-west-2",
        "sourceIPAddress": "192.0.2.0",
        "userAgent": "console.aws.amazon.com",
        "requestParameters": {"tableName": "Music"},
        "responseElements": null,
        "requestID": "DISTSH6DQRLCC74L48Q51LRBHFVV4KQNSO5AEMVJF66Q9ASUAAJG",
        "eventID": "c07befa7-f402-4770-8c1b-1911601ed2af",
        "eventType": "AwsApiCall",
        "apiVersion": "2012-08-10",
        "recipientAccountId": "111122223333"
    },

    {
        "eventVersion": "1.03",
        "userIdentity": {
            "type": "AssumedRole",
            "principalId": "AKIAIOSFODNN7EXAMPLE:bob",
            "arn": "arn:aws:sts::111122223333:assumed-role/users/bob",
            "accountId": "111122223333",
            "accessKeyId": "AKIAIOSFODNN7EXAMPLE",
            "sessionContext": {
                "attributes": {
                    "mfaAuthenticated": "false",
                    "creationDate": "2015-05-28T18:06:01Z"
                },
                "sessionIssuer": {
                    "type": "Role",
                    "principalId": "AKIAI44QH8DHBEXAMPLE",
                    "arn": "arn:aws:iam::444455556666:role/admin-role",
                    "accountId": "444455556666",
                    "userName": "bob"
                }
            }
        },
        "eventTime": "2015-05-04T02:14:52Z",
        "eventSource": "dynamodb.amazonaws.com",
        "eventName": "UpdateTable",
        "awsRegion": "us-west-2",
        "sourceIPAddress": "192.0.2.0",
        "userAgent": "console.aws.amazon.com",
        "requestParameters": {"provisionedThroughput": {
            "writeCapacityUnits": 25,
            "readCapacityUnits": 25
        }},
        "responseElements": {"tableDescription": {
            "tableName": "Music",
            "attributeDefinitions": [
                {
                    "attributeType": "S",
                    "attributeName": "Artist"
                },
                {
                    "attributeType": "S",
                    "attributeName": "SongTitle"
                }
            ],
            "itemCount": 0,
            "provisionedThroughput": {
                "writeCapacityUnits": 10,
                "numberOfDecreasesToday": 0,
                "readCapacityUnits": 10,
                "lastIncreaseDateTime": "May 3, 2015 11:34:14 PM"
            },
            "creationDateTime": "May 3, 2015 11:34:14 PM",
            "keySchema": [
                {
                    "attributeName": "Artist",
                    "keyType": "HASH"
                },
                {
                    "attributeName": "SongTitle",
                    "keyType": "RANGE"
                }
            ],
            "tableStatus": "UPDATING",
            "tableSizeBytes": 0
        }},
        "requestID": "AALNP0J2L244N5O15PKISJ1KUFVV4KQNSO5AEMVJF66Q9ASUAAJG",
        "eventID": "eb834e01-f168-435f-92c0-c36278378b6e",
        "eventType": "AwsApiCall",
        "apiVersion": "2012-08-10",
        "recipientAccountId": "111122223333"
    },

    {
        "eventVersion": "1.03",
        "userIdentity": {
            "type": "AssumedRole",
            "principalId": "AKIAIOSFODNN7EXAMPLE:bob",
            "arn": "arn:aws:sts::111122223333:assumed-role/users/bob",
            "accountId": "111122223333",
            "accessKeyId": "AKIAIOSFODNN7EXAMPLE",
            "sessionContext": {
                "attributes": {
                    "mfaAuthenticated": "false",
                    "creationDate": "2015-05-28T18:06:01Z"
                },
                "sessionIssuer": {
                    "type": "Role",
                    "principalId": "AKIAI44QH8DHBEXAMPLE",
                    "arn": "arn:aws:iam::444455556666:role/admin-role",
                    "accountId": "444455556666",
                    "userName": "bob"
                }
            }
        },
        "eventTime": "2015-05-04T02:42:20Z",
        "eventSource": "dynamodb.amazonaws.com",
        "eventName": "ListTables",
        "awsRegion": "us-west-2",
        "sourceIPAddress": "192.0.2.0",
        "userAgent": "console.aws.amazon.com",
        "requestParameters": null,
        "responseElements": null,
        "requestID": "3BGHST5OVHLMTPUMAUTA1RF4M3VV4KQNSO5AEMVJF66Q9ASUAAJG",
        "eventID": "bd5bf4b0-b8a5-4bec-9edf-83605bd5e54e",
        "eventType": "AwsApiCall",
        "apiVersion": "2012-08-10",
        "recipientAccountId": "111122223333"
    },

    {
        "eventVersion": "1.03",
        "userIdentity": {
            "type": "AssumedRole",
            "principalId": "AKIAIOSFODNN7EXAMPLE:bob",
            "arn": "arn:aws:sts::111122223333:assumed-role/users/bob",
            "accountId": "111122223333",
            "accessKeyId": "AKIAIOSFODNN7EXAMPLE",
            "sessionContext": {
                "attributes": {
                    "mfaAuthenticated": "false",
                    "creationDate": "2015-05-28T18:06:01Z"
                },
                "sessionIssuer": {
                    "type": "Role",
                    "principalId": "AKIAI44QH8DHBEXAMPLE",
                    "arn": "arn:aws:iam::444455556666:role/admin-role",
                    "accountId": "444455556666",
                    "userName": "bob"
                }
            }
        },
        "eventTime": "2015-05-04T13:38:20Z",
        "eventSource": "dynamodb.amazonaws.com",
        "eventName": "DeleteTable",
        "awsRegion": "us-west-2",
        "sourceIPAddress": "192.0.2.0",
        "userAgent": "console.aws.amazon.com",
        "requestParameters": {"tableName": "Music"},
        "responseElements": {"tableDescription": {
            "tableName": "Music",
            "itemCount": 0,
            "provisionedThroughput": {
                "writeCapacityUnits": 25,
                "numberOfDecreasesToday": 0,
                "readCapacityUnits": 25
            },
            "tableStatus": "DELETING",
            "tableSizeBytes": 0
        }},
        "requestID": "4KBNVRGD25RG1KEO9UT4V3FQDJVV4KQNSO5AEMVJF66Q9ASUAAJG",
        "eventID": "a954451c-c2fc-4561-8aea-7a30ba1fdf52",
        "eventType": "AwsApiCall",
        "apiVersion": "2012-08-10",
        "recipientAccountId": "111122223333"
    }
]}
```