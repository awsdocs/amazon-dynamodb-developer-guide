# Enabling Time To Live<a name="time-to-live-ttl-how-to"></a>

This section describes how to use the DynamoDB console or CLI to enable Time To Live\. To use the API instead, see [Amazon DynamoDB API Reference](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/)\.

**Topics**
+ [Enable Time To Live \(console\)](#time-to-live-ttl-how-to-enable-console)
+ [Enable Time To Live \(CLI\)](#time-to-live-ttl-how-to-enable-cli-sdk)

## Enable Time To Live \(console\)<a name="time-to-live-ttl-how-to-enable-console"></a>

To enable Time To Live using the DynamoDB console:

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/home](https://console.aws.amazon.com/dynamodb/home)\. 

1. Choose **Tables** and then choose the table that you want to modify\.

1. In **Table details**, next to **TTL attribute**, choose **Manage TTL**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/ttl_table.png)

1. In the **Manage TTL** dialog box, choose **Enable TTL** and then type the **TTL attribute** name\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/ttl_manage.png)

   There are three settings in **Manage TTL**:
   + **Enable TTL** – Choose this to either enable or disable TTL on the table\. It may take up to one hour for the change to fully process\.
   + **TTL Attribute** – The name of the DynamoDB attribute to store the TTL timestamp for items\.
   + **24\-hour backup streams** – Choose this to enable Amazon DynamoDB Streams on the table\. For more information about how you can use DynamoDB Streams for backup, see [DynamoDB Streams and Time To Live](time-to-live-ttl-streams.md)\.

1. \(Optional\) To preview some of the items that will be deleted when TTL is enabled, choose **Run preview**\. 
**Warning**  
 This provides you with a sample list of items\. It does not provide you with a complete list of items that will be deleted by TTL\.

1. Choose **Continue** to save the settings and enable TTL\.

Now that TTL is enabled, the TTL attribute is marked **TTL** when you view items in the DynamoDB console\.

You can view the date and time that an item will expire by hovering your mouse over the attribute\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/ttl_items.png)

## Enable Time To Live \(CLI\)<a name="time-to-live-ttl-how-to-enable-cli-sdk"></a>

 To enable TTL on the "TTLExample" table: 

```
aws dynamodb update-time-to-live --table-name TTLExample --time-to-live-specification "Enabled=true, AttributeName=ttl"
```

 To describe TTL on the "TTLExample" table: 

```
aws dynamodb describe-time-to-live --table-name TTLExample
{
    "TimeToLiveDescription": {
        "AttributeName": "ttl", 
        "TimeToLiveStatus": "ENABLED"
    }
}
```

 To add an item to the "TTLExample" table with the Time To Live attribute set using the BASH shell and CLI: 

```
EXP=`date -d '+5 days' +%s`
aws dynamodb put-item --table-name "TTLExample" --item '{"id": {"N": "1"}, "ttl": {"N": "'$EXP'"}}'
```

 This example started with the current date and added five days to it to create an expiration time\. Then, it converts the expiration time to epoch time format to finally add an item to the "TTLExample" table\. 

**Note**  
 One way to set expiration values for Time To Live is to calculate the number of seconds to add to the expiration time\. For example, five days is 432000 seconds\. However, it is often preferable to start with a date and work from there\. 

It is fairly simple to get the current time in epoch time format\. For example:
+ Linux Terminal: `date +%s`
+ Python: `import time; long(time.time())`
+ Java: `System.currentTimeMillis() / 1000L`
+ JavaScript: `Math.floor(Date.now() / 1000)`