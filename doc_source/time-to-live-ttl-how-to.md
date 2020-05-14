# Enabling Time to Live \(TTL\)<a name="time-to-live-ttl-how-to"></a>

You can use the Amazon DynamoDB console or the AWS Command Line Interface \(AWS CLI\) to enable Time to Live\. To use the API instead, see [Amazon DynamoDB API Reference](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/)\.

**Topics**
+ [Enable Time to Live \(Console\)](#time-to-live-ttl-how-to-enable-console)
+ [Enable Time to Live \(AWS CLI\)](#time-to-live-ttl-how-to-enable-cli-sdk)

## Enable Time to Live \(Console\)<a name="time-to-live-ttl-how-to-enable-console"></a>

Follow these steps to enable Time to Live using the DynamoDB console:

1. Sign in to the AWS Management Console and open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. Choose **Tables**, and then choose the table that you want to modify\.

1. In **Table details**, next to **TTL attribute**, choose **Manage TTL**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/ttl_table.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

1. In the **Manage TTL** dialog box, choose **Enable TTL**, and then enter the **TTL attribute** name\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/ttl_manage.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

   There are three settings in **Manage TTL**:
   + **Enable TTL** – Choose this to either enable or disable TTL on the table\. It can take up to one hour for the change to fully process\.
   + **TTL Attribute** – The name of the DynamoDB attribute to store the TTL timestamp for items\.
   + **24\-hour backup streams** – Choose this setting to enable Amazon DynamoDB Streams on the table\. For more information about how you can use DynamoDB Streams for backup, see [DynamoDB Streams and Time to Live](time-to-live-ttl-streams.md)\.

1. \(Optional\) To preview some of the items that will be deleted when TTL is enabled, choose **Run preview**\. 
**Warning**  
 This provides you with a sample list of items\. It does not provide you with a complete list of items that will be deleted by TTL\.

1. Choose **Continue** to save the settings and enable TTL\.

Now that TTL is enabled, the TTL attribute is marked **TTL** when you view items on the DynamoDB console\.

You can view the date and time that an item expires by hovering your pointer over the attribute\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/ttl_items.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

## Enable Time to Live \(AWS CLI\)<a name="time-to-live-ttl-how-to-enable-cli-sdk"></a>

1. Enable TTL on the `TTLExample` table\.

   ```
   aws dynamodb update-time-to-live --table-name TTLExample --time-to-live-specification "Enabled=true, AttributeName=ttl"
   ```

1. Describe TTL on the `TTLExample` table\.

   ```
   aws dynamodb describe-time-to-live --table-name TTLExample
   {
       "TimeToLiveDescription": {
           "AttributeName": "ttl",
           "TimeToLiveStatus": "ENABLED"
       }
   }
   ```

1. Add an item to the `TTLExample` table with the Time to Live attribute set using the BASH shell and the AWS CLI\. 

   ```
   EXP=`date -d '+5 days' +%s`
   aws dynamodb put-item --table-name "TTLExample" --item '{"id": {"N": "1"}, "ttl": {"N": "'$EXP'"}}'
   ```

This example starts with the current date and adds 5 days to it to create an expiration time\. Then, it converts the expiration time to epoch time format to finally add an item to the "`TTLExample`" table\. 

**Note**  
 One way to set expiration values for Time to Live is to calculate the number of seconds to add to the expiration time\. For example, 5 days is 432,000 seconds\. However, it is often preferable to start with a date and work from there\.

It is fairly simple to get the current time in epoch time format, as in the following examples\.
+ Linux Terminal: `date +%s`
+ Python: `import time; int(time.time())`
+ Java: `System.currentTimeMillis() / 1000L`
+ JavaScript: `Math.floor(Date.now() / 1000)`