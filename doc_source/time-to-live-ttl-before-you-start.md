# Using DynamoDB Time to Live \(TTL\)<a name="time-to-live-ttl-before-you-start"></a>

 When using TTL, most of the hard work is done behind the scenes by DynamoDB on your behalf\. You should, though, be aware of a few considerations to help your implementation proceed smoothly\.

**Topics**
+ [Formatting an item’s TTL attribute](#time-to-live-ttl-before-you-start-formatting)
+ [Usage Notes](#time-to-live-ttl-before-you-start-notes)
+ [Troubleshooting TTL](#time-to-live-ttl-before-you-start-troubleshooting)

## Formatting an item’s TTL attribute<a name="time-to-live-ttl-before-you-start-formatting"></a>

When enabling TTL on a table, DynamoDB requires you to identify a specific attribute name that the service will look for when determining if an item is eligible for expiration\. In addition, further requirements ensure that the background TTL processes uses the value of the TTL attribute\. If an item is to be eligible for expiration via TTL:
+ The item must contain the attribute specified when TTL was enabled on the table\. For example, if you specify for a table to use the attribute name `expdate` as the TTL attribute, but an item does not have an attribute with that name, the TTL process ignores the item\.
+ The TTL attribute’s value must be a `Number` data type\. For example, if you specify for a table to use the attribute name `expdate` as the TTL attribute, but the attribute on an item is a `String` data type, the TTL processes ignore the item\.
+ The TTL attribute’s value must be a timestamp in [Unix epoch time format](https://en.wikipedia.org/wiki/Unix_time) in *seconds*\. If you use any other format, the TTL processes ignore the item\. For example, if you set the value of the attribute to 1645119622, that is Thursday, February 17, 2022 17:40:22 \(GMT\), the item will be expired after that time\. For help formatting your epoch timestamps, you can use third\-party tools such as [Epoch Converter](https://epochconverter.com/) to get a visual web form\.
+ The TTL attribute value must be a datetimestamp with an expiration of no more than five years in the past\. For example, if you set the value of the attribute to 1171734022, that is February 17, 2007 17:40:22 \(GMT\) and older than five years\. As a result, the TTL processes will not expire that item\.

## Usage Notes<a name="time-to-live-ttl-before-you-start-notes"></a>

When using TTL, consider the following:
+ Enabling, disabling, or changing TTL settings on a table can take approximately one hour for the settings to propagate and to allow the execution of any further TTL related actions\.
+ You cannot reconfigure TTL to look for a different attribute\. You must disable TTL, and then reenable TTL with the new attribute going forward\.
+ When you use AWS CloudFormation, you can enable TTL when creating a DynamoDB table\. For more information, see the [AWS CloudFormation User Guide](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-dynamodb-table.html)\.
+ You can use AWS Identity and Access Management \(IAM\) policies to prevent unauthorized updates to the TTL attribute on an item or the configuration of TTL\. If you allow access to only specified actions in your existing IAM policies, ensure that your policies are updated to allow `dynamodb:UpdateTimeToLive` for roles that need to enable or disable TTL on tables\. For more information, see [Using Identity\-Based Policies \(IAM Policies\) for Amazon DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/using-identity-based-policies.html)\.
+ Consider whether you need to do any post processing of deleted items via Amazon DynamoDB Streams, such as archiving items to an Amazon S3 data lake\. The streams records of TTL deletes are marked as system deletes instead of normal deletes, and you can filter for system deletes by using an AWS Lambda function\. For more information about the additions to the streams records, see [Amazon DynamoDB Streams and Time to Live](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/time-to-live-ttl-streams.html)\.
+ If data recovery is a concern, we recommend that you back up your tables\.
  + For fully managed table backups, use DynamoDB [on\-demand backups](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/BackupRestore.html) or [continuous backups with point\-in\-time recovery](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/PointInTimeRecovery.html)\.
  + For a 24\-hour recovery window, you can use Amazon DynamoDB Streams\. For more information, see [Amazon DynamoDB Streams and Time to Live](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/time-to-live-ttl-streams.html)\.

## Troubleshooting TTL<a name="time-to-live-ttl-before-you-start-troubleshooting"></a>

If DynamoDB TTL is not working, check the following:
+ Confirm that you have enabled TTL on the table, and the name of the attribute selected for TTL is set to what your code is writing into items\. You can confirm this information on a table’s *Overview* tab on the DynamoDB console\.
+ Look at Amazon CloudWatch metrics on the *Metrics* tab of the DynamoDB console to confirm that TTL is deleting items as you expect them to be deleted\.
+ Confirm that the TTL attribute value is properly formatted\. For more information see, [Formatting an item’s TTL attribute](#time-to-live-ttl-before-you-start-formatting)\.