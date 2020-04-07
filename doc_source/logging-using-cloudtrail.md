# Logging DynamoDB Operations by Using AWS CloudTrail<a name="logging-using-cloudtrail"></a>

DynamoDB is integrated with AWS CloudTrail, a service that provides a record of actions taken by a user, role, or an AWS service in DynamoDB\. CloudTrail captures all API calls for DynamoDB as events\. The calls captured include calls from the DynamoDB console and code calls to the DynamoDB API operations\. If you create a trail, you can enable continuous delivery of CloudTrail events to an Amazon S3 bucket, including events for DynamoDB\. If you don't configure a trail, you can still view the most recent events in the CloudTrail console in **Event history**\. Using the information collected by CloudTrail, you can determine the request that was made to DynamoDB, the IP address from which the request was made, who made the request, when it was made, and additional details\. 

To learn more about CloudTrail, including how to configure and enable it, see the [AWS CloudTrail User Guide](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/)\.

## DynamoDB Information in CloudTrail<a name="service-name-info-in-cloudtrail"></a>

CloudTrail is enabled on your AWS account when you create the account\. When supported event activity occurs in DynamoDB, that activity is recorded in a CloudTrail event along with other AWS service events in **Event history**\. You can view, search, and download recent events in your AWS account\. For more information, see [Viewing Events with CloudTrail Event History](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/view-cloudtrail-events.html)\. 

For an ongoing record of events in your AWS account, including events for DynamoDB, create a trail\. A *trail* enables CloudTrail to deliver log files to an Amazon S3 bucket\. By default, when you create a trail in the console, the trail applies to all AWS Regions\. The trail logs events from all Regions in the AWS partition and delivers the log files to the Amazon S3 bucket that you specify\. Additionally, you can configure other AWS services to further analyze and act upon the event data collected in CloudTrail logs\. For more information, see the following: 
+ [Overview for Creating a Trail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-and-update-a-trail.html)
+ [CloudTrail Supported Services and Integrations](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-aws-service-specific-topics.html#cloudtrail-aws-service-specific-topics-integrations)
+ [Configuring Amazon SNS Notifications for CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/getting_notifications_top_level.html)
+ [Receiving CloudTrail Log Files from Multiple Regions](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/receive-cloudtrail-log-files-from-multiple-regions.html) and [Receiving CloudTrail Log Files from Multiple Accounts](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-receive-logs-from-multiple-accounts.html)

The following API actions are logged as events in CloudTrail files:

** Amazon DynamoDB **
+ [CreateBackup](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_CreateBackup.html)
+ [CreateGlobalTable](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_CreateGlobalTable.html)
+ [CreateTable](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_CreateTable.html)
+ [DeleteBackup](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DeleteBackup.html)
+ [DeleteTable](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DeleteTable.html)
+ [DescribeBackup](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DescribeBackup.html)
+ [DescribeContinuousBackups](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DescribeContinuousBackups.html)
+ [DescribeGlobalTable](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DescribeGlobalTable.html)
+ [DescribeLimits](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DescribeLimits.html)
+ [DescribeTable](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DescribeTable.html)
+ [DescribeTimeToLive](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DescribeTimeToLive.html)
+ [ListBackups](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_ListBackups.html)
+ [ListTables](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_ListTables.html)
+ [ListTagsOfResource](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_ListTagsOfResource.html)
+ [ListGlobalTables](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_ListGlobalTables.html)
+ [RestoreTableFromBackup](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_RestoreTableFromBackup.html)
+ [RestoreTableToPointInTime](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_RestoreTableToPointInTime.html)
+ [TagResource](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_TagResource.html)
+ [UntagResource](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_UntagResource.html)
+ [UpdateGlobalTable](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_UpdateGlobalTable.html)
+ [UpdateTable](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_UpdateTable.html)
+ [UpdateTimeToLive](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_UpdateTimeToLive.html)
+ DescribeReservedCapacity
+ DescribeReservedCapacityOfferings
+ DescribeScalableTargets
+ RegisterScalableTarget
+ PurchaseReservedCapacityOfferings

** DynamoDB Streams **
+ [DescribeStream](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_streams_DescribeStream.html)
+ [ListStreams](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_streams_ListStreams.html)

** DynamoDB Accelerator \(DAX\) **
+ [CreateCluster](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_CreateCluster.html)
+ [CreateParameterGroup](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_CreateParameterGroup.html)
+ [CreateSubnetGroup](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_CreateSubnetGroup.html)
+ [DecreaseReplicationFactor](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_DecreaseReplicationFactor.html)
+ [DeleteCluster](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_DeleteCluster.html)
+ [DeleteParameterGroup](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_DeleteParameterGroup.html)
+ [DeleteSubnetGroup](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_DeleteSubnetGroup.html)
+ [DescribeClusters](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_DescribeClusters.html)
+ [DescribeDefaultParameters](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_DescribeDefaultParameters.html)
+ [DescribeEvents](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_DescribeEvents.html)
+ [DescribeParameterGroups](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_DescribeParameterGroups.html)
+ [DescribeParameters](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_DescribeParameters.html)
+ [DescribeSubnetGroups](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_DescribeSubnetGroups.html)
+ [IncreaseReplicationFactor](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_IncreaseReplicationFactor.html)
+ [ListTags](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_ListTags.html)
+ [RebootNode](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_RebootNode.html)
+ [TagResource](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_TagResource.html)
+ [UntagResource](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_UntagResource.html)
+ [UpdateCluster](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_UpdateCluster.html)
+ [UpdateParameterGroup](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_UpdateParameterGroup.html)
+ [UpdateSubnetGroup](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_dax_UpdateSubnetGroup.html)

Every event or log entry contains information about who generated the request\. The identity information helps you determine the following: 
+ Whether the request was made with root or AWS Identity and Access Management \(IAM\) user credentials\.
+ Whether the request was made with temporary security credentials for a role or federated user\.
+ Whether the request was made by another AWS service\.

For more information, see the [CloudTrail userIdentity Element](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference-user-identity.html)\.

## Understanding DynamoDB Log File Entries<a name="understanding-service-name-entries"></a>

 A trail is a configuration that enables delivery of events as log files to an Amazon S3 bucket that you specify\. CloudTrail log files contain one or more log entries\. An event represents a single request from any source and includes information about the requested action, the date and time of the action, request parameters, and so on\. CloudTrail log files aren't an ordered stack trace of the public API calls, so they don't appear in any specific order\. 

The following example shows a CloudTrail log that demonstrates the `CreateTable`, `DescribeTable`, `UpdateTable`, `ListTables`, `DeleteTable`, and `CreateCluster` actions\.

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
    },

    {
    "eventVersion": "1.05",
    "userIdentity": {
        "type": "IAMUser",
        "principalId": "AIDAIAVK7DT3VEXAMPLES",
        "arn": "arn:aws:iam::111122223333:user/bob",
        "accountId": "111122223333",
        "accessKeyId": "AKIAIOSFODNN7EXAMPLE",
        "userName": "bob"
    },
    "eventTime": "2019-12-17T23:17:34Z",
    "eventSource": "dax.amazonaws.com",
    "eventName": "CreateCluster",
    "awsRegion": "us-west-2",
    "sourceIPAddress": "205.251.233.48",
    "userAgent": "aws-cli/1.16.304 Python/3.6.9 Linux/4.9.184-0.1.ac.235.83.329.metal1.x86_64 botocore/1.13.40",
    "requestParameters": {
        "sSESpecification": {
            "enabled": true
        },
        "clusterName": "daxcluster",
        "nodeType": "dax.r4.large",
        "replicationFactor": 3,
        "iamRoleArn": "arn:aws:iam::111122223333:role/DAXServiceRoleForDynamoDBAccess"
    },
    "responseElements": {
        "cluster": {
            "securityGroups": [
                {
                    "securityGroupIdentifier": "sg-1af6e36e",
                    "status": "active"
                }
            ],
            "parameterGroup": {
                "nodeIdsToReboot": [],
                "parameterGroupName": "default.dax1.0",
                "parameterApplyStatus": "in-sync"
            },
            "clusterDiscoveryEndpoint": {
                "port": 8111
            },
            "clusterArn": "arn:aws:dax:us-west-2:111122223333:cache/daxcluster",
            "status": "creating",
            "subnetGroup": "default",
            "sSEDescription": {
                "status": "ENABLED",
                "kMSMasterKeyArn": "arn:aws:kms:us-west-2:111122223333:key/764898e4-adb1-46d6-a762-e2f4225b4fc4"
            },
            "iamRoleArn": "arn:aws:iam::111122223333:role/DAXServiceRoleForDynamoDBAccess",
            "clusterName": "daxcluster",
            "activeNodes": 0,
            "totalNodes": 3,
            "preferredMaintenanceWindow": "thu:13:00-thu:14:00",
            "nodeType": "dax.r4.large"
        }},
        "requestID": "585adc5f-ad05-4e27-8804-70ba1315f8fd",
        "eventID": "29158945-28da-4e32-88e1-56d1b90c1a0c",
        "eventType": "AwsApiCall",
        "recipientAccountId": "111122223333"
    }
]}
```