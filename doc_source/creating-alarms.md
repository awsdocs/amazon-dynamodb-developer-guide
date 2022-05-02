# Creating CloudWatch Alarms to Monitor DynamoDB<a name="creating-alarms"></a>

You can create a CloudWatch alarm that sends an Amazon SNS message when the alarm changes state\. An alarm watches a single metric over a time period you specify, and performs one or more actions based on the value of the metric relative to a given threshold over a number of time periods\. The action is a notification sent to an Amazon SNS topic or Auto Scaling policy\. Alarms invoke actions for sustained state changes only\. CloudWatch alarms do not invoke actions simply because they are in a particular state; the state must have changed and been maintained for a specified number of periods\.

**Note**  
You must specify all the required dimensions when creating your CloudWatch alarm, since CloudWatch will not aggregate metrics for a missing dimension\. Creating a CloudWatch alarm with a missing dimension will not result in an error, when creating the alarm\.  
For a list of supported metrics and their required dimensions in DynamoDB, see [Viewing metrics and dimensions](metrics-dimensions.md#dynamodb-metrics-dimensions)\.

## How can I be notified before I consume my entire read capacity?<a name="notify-reach-capacity"></a>

1. Create an Amazon SNS topic, `arn:aws:sns:us-east-1:123456789012:capacity-alarm`\.

   For more information, see [Set Up Amazon Simple Notification Service](http://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/US_SetupSNS.html)\.

1. Create the alarm\. In this example, we assume a provisioned capacity of five read capacity units\.

   ```
   aws cloudwatch put-metric-alarm \
       --alarm-name ReadCapacityUnitsLimitAlarm \
       --alarm-description "Alarm when read capacity reaches 80% of my provisioned read capacity" \
       --namespace AWS/DynamoDB \
       --metric-name ConsumedReadCapacityUnits \
       --dimensions Name=TableName,Value=myTable \
       --statistic Sum \
       --threshold 240 \
       --comparison-operator GreaterThanOrEqualToThreshold \
       --period 60 \                           
       --evaluation-periods 1 \
       --alarm-actions arn:aws:sns:us-east-1:123456789012:capacity-alarm
   ```

1. Test the alarm\.

   ```
   aws cloudwatch set-alarm-state --alarm-name ReadCapacityUnitsLimitAlarm --state-reason "initializing" --state-value OK
   ```

   ```
   aws cloudwatch set-alarm-state --alarm-name ReadCapacityUnitsLimitAlarm --state-reason "initializing" --state-value ALARM
   ```

**Note**  
The alarm is activated whenever the consumed read capacity is at least 4 units per second \(80% of provisioned read capacity of 5\) for 1 minute \(60 seconds\)\. So the `threshold` is 240 read capacity units \(4 units/sec \* 60 seconds\)\. Any time the read capacity is updated you should update the alarm calculations appropriately\. You can avoid this process by creating alarms through the DynamoDB Console\. In this way, the alarms are automatically updated for you\.

## How can I be notified if any requests exceed the provisioned throughput quotas of a table?<a name="notify-exceed-throughput"></a>

1. Create an Amazon SNS topic, `arn:aws:sns:us-east-1:123456789012:requests-exceeding-throughput`\.

   For more information, see [Set Up Amazon Simple Notification Service](http://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/US_SetupSNS.html)\.

1. Create the alarm\.

   ```
   aws cloudwatch put-metric-alarm \
       --alarm-name RequestsExceedingThroughputAlarm\
       --alarm-description "Alarm when my requests are exceeding provisioned throughput quotas of a table" \
       --namespace AWS/DynamoDB \
       --metric-name ThrottledRequests \
       --dimensions Name=TableName,Value=myTable Name=Operation,Value=aDynamoDBOperation \
       --statistic Sum \
       --threshold 0 \
       --comparison-operator GreaterThanThreshold \
       --period 300 \
       --unit Count \
       --evaluation-periods 1 \
       --alarm-actions arn:aws:sns:us-east-1:123456789012:requests-exceeding-throughput
   ```

1. Test the alarm\.

   ```
   aws cloudwatch set-alarm-state --alarm-name RequestsExceedingThroughputAlarm --state-reason "initializing" --state-value OK
   ```

   ```
   aws cloudwatch set-alarm-state --alarm-name RequestsExceedingThroughputAlarm --state-reason "initializing" --state-value ALARM
   ```

## How can I be notified if any system errors occurred?<a name="notify-system-errors"></a>

1. Create an Amazon SNS topic, `arn:aws:sns:us-east-1:123456789012:notify-on-system-errors`\.

   For more information, see [Set Up Amazon Simple Notification Service](http://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/US_SetupSNS.html)\.

1. Create the alarm\.

   ```
   aws cloudwatch put-metric-alarm \
       --alarm-name SystemErrorsAlarm \
       --alarm-description "Alarm when system errors occur" \
       --namespace AWS/DynamoDB \
       --metric-name SystemErrors \
       --dimensions Name=TableName,Value=myTable Name=Operation,Value=aDynamoDBOperation \
       --statistic Sum \
       --threshold 0 \
       --comparison-operator GreaterThanThreshold \
       --period 60 \
       --unit Count \
       --evaluation-periods 1 \
       --alarm-actions arn:aws:sns:us-east-1:123456789012:notify-on-system-errors
   ```

1. Test the alarm\.

   ```
   aws cloudwatch set-alarm-state --alarm-name SystemErrorsAlarm --state-reason "initializing" --state-value OK
   ```

   ```
   aws cloudwatch set-alarm-state --alarm-name SystemErrorsAlarm --state-reason "initializing" --state-value ALARM
   ```