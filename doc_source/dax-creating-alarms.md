# Creating CloudWatch Alarms to Monitor DAX<a name="dax-creating-alarms"></a>

You can create an Amazon CloudWatch alarm that sends an Amazon Simple Notification Service \(Amazon SNS\) message when the alarm changes state\. An alarm watches a single metric over a time period that you specify\. It performs one or more actions based on the value of the metric relative to a given threshold over a number of time periods\. The action is a notification that is sent to an Amazon SNS topic or Auto Scaling policy\. Alarms invoke actions for sustained state changes only\. CloudWatch alarms do not invoke actions simply because they are in a particular state\. The state must have changed and been maintained for a specified number of periods\.

## How Can I Be Notified of Query Cache Misses?<a name="dax-notify-reach-capacity"></a>

1. Create an Amazon SNS topic, `arn:aws:sns:us-west-2:522194210714:QueryMissAlarm`\.

   For more information, see [Set Up Amazon Simple Notification Service](http://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/US_SetupSNS.html) in the *Amazon CloudWatch User Guide*\.

1. Create the alarm\.

   ```
   aws cloudwatch put-metric-alarm \
       --alarm-name QueryCacheMissesAlarm  \
       --alarm-description "Alarm over query cache misses" \
       --namespace AWS/DAX \
       --metric-name QueryCacheMisses  \
       --dimensions Name=ClusterID,Value=myCluster \
       --statistic Sum \
       --threshold 8 \
       --comparison-operator GreaterThanOrEqualToThreshold \
       --period 60 \
       --evaluation-periods 1 \
       --alarm-actions arn:aws:sns:us-west-2:522194210714:QueryMissAlarm
   ```

1. Test the alarm\.

   ```
   aws cloudwatch set-alarm-state --alarm-name QueryCacheMissesAlarm --state-reason "initializing" --state-value OK
   ```

   ```
   aws cloudwatch set-alarm-state --alarm-name QueryCacheMissesAlarm --state-reason "initializing" --state-value ALARM
   ```

**Note**  
 You can increase or decrease the threshold to one that makes sense for your application\. You can also use [CloudWatch Metric Math](http://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/using-metric-math.html) to define a cache miss rate metric and set an alarm over that metric\. 

## How Can I Be Notified If Requests Cause Any Internal Error in the Cluster?<a name="dax-notify-system-errors"></a>

1. Create an Amazon SNS topic, `arn:aws:sns:us-west-2:123456789012:notify-on-system-errors`\.

   For more information, see [Set Up Amazon Simple Notification Service](http://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/US_SetupSNS.html) in the *Amazon CloudWatch User Guide*\.

1. Create the alarm\.

   ```
   aws cloudwatch put-metric-alarm \
       --alarm-name FaultRequestCountAlarm \
       --alarm-description "Alarm when a request causes an internal error" \
       --namespace AWS/DAX \
       --metric-name FaultRequestCount \
       --dimensions Name=ClusterID,Value=myCluster \
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
   aws cloudwatch set-alarm-state --alarm-name FaultRequestCountAlarm --state-reason "initializing" --state-value OK
   ```

   ```
   aws cloudwatch set-alarm-state --alarm-name FaultRequestCountAlarm --state-reason "initializing" --state-value ALARM
   ```