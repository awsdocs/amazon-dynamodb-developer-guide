# Using the console<a name="ConsoleDynamoDB"></a>

You can access the AWS Management Console for Amazon DynamoDB at [https://console\.aws\.amazon\.com/dynamodb/home](https://console.aws.amazon.com/dynamodb/home)\.

You can use the console to do the following in DynamoDB:
+ Monitor recent alerts, total capacity, service health, and the latest DynamoDB news on the DynamoDB dashboard\. 
+ Create, update, and delete tables\. The capacity calculator provides estimates of how many capacity units to request based on the usage information you provide\.
+ Manage streams\.
+ View, add, update, and delete items that are stored in tables\. Manage Time to Live \(TTL\) to define when items in a table expire so that they can be automatically deleted from the database\.
+ Query and scan a table\.
+ Set up and view alarms to monitor your table's capacity usage\. View your table's top monitoring metrics on real\-time graphs from CloudWatch\.
+ Modify a table's provisioned capacity\.
+ Modify a table's table class\.
+ Create and delete global secondary indexes\.
+ Create triggers to connect DynamoDB streams to AWS Lambda functions\.
+ Apply tags to your resources to help organize and identify them\.
+ Purchase reserved capacity\.

The console displays an introductory screen that prompts you to create your first table\. To view your tables, in the navigation pane on the left side of the console, choose **Tables**\.

Here's a high\-level overview of the actions available per table within each navigation tab:
+ **Overview** – View table details, including item count and metrics\.
+ **Indexes** – Manage global and local secondary indexes\.
+ **Monitor** – View alarms, CloudWatch Contributor Insights, and Cloudwatch metrics\.
+ **Global tables** – Manage table replicas\.
+ **Backups** – Manage point\-in\-time recovery and on\-demand backups\.
+ **Exports and streams** – Export your table to Amazon S3 and manage DynamoDB Streams and Kinesis Data Streams\.
+ **Additional settings** – Manage read/write capacity, Time to Live settings, encryption, and tags\.