# Getting started with CloudWatch contributor insights for DynamoDB<a name="contributorinsights_tutorial"></a>

This section describes how to use Amazon CloudWatch Contributor Insights with the Amazon DynamoDB console or the AWS Command Line Interface \(AWS CLI\)\. 

In the following examples, you use the DynamoDB table that is defined in the [Getting started with DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GettingStartedDynamoDB.html) tutorial\.

**Topics**
+ [Using contributor insights \(console\)](#usecontributorinsights_console)
+ [Using contributor insights \(AWS CLI\)](#usecontributorinsights_cli)

## Using contributor insights \(console\)<a name="usecontributorinsights_console"></a>

1. Sign in to the AWS Management Console and open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. In the navigation pane on the left side of the console, choose **Tables**\.

1. Choose the `Music` table\.

1. Choose the **Contributor Insights** tab\.

1. Choose **Manage Contributor Insights**\.  
![\[Console screenshot showing Contributor Insights tab and button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/CI_ChooseAndManage.png)

1. In the **Manage Contributor Insights** dialog box, under **Contributor Insights Status**, choose **Enabled** for both the `Music` base table and the `AlbumTitle-index` global secondary index\. Then choose **Confirm**\.  
![\[Console screenshot showing Contributor Insights status list options.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/CI_Enable.png)

   If the operation fails, see [DescribeContributorInsights FailureException](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DescribeContributorInsights.html#DDB-DescribeContributorInsights-response-FailureException) in the *Amazon DynamoDB API Reference* for possible reasons\.

1. Choose **View in DynamoDB**\.  
![\[Console screenshot showing View in DynamoDB button in the Contributor Insights settings.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/CI_ViewInDynamoDB.png)

1. The Contributor Insights graphs are now visible on the **Contributor Insights** tab for the `Music` table\.   
![\[Console screenshot showing Contributor Insights tab with several graphs for the music table.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/CI_Graphs.png)

### Creating CloudWatch alarms<a name="usecontributorinsights_console_alarms"></a>

Follow these steps to create a CloudWatch alarm and be notified when any partition key consumes more than 50,000 [ConsumedThroughputUnits](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/contributorinsights_HowItWorks.html#contributorinsights_HowItWorks.Graphs.most-accessed)\.

1. Sign in to the AWS Management Console and open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)

1. In the navigation pane on the left side of the console, choose **Contributor Insights**\.

1. Choose the **DynamoDBContributorInsights\-PKC\-Music** rule\.

1. Choose the **Actions** drop down\.

1. Choose **View in metrics**\.

1. Choose **Max Contributor Value**\.
**Note**  
Only `Max Contributor Value` and `Maximum` return useful statistics\. The other statistics in this list don't return meaningful values\.  
![\[Console screenshot showing Contributor Insights tab and button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/CI_AlarmsViewinMetrics.png)

1. On the **Actions** column, Choose **Create Alarm**\.  
![\[Console screenshot showing Contributor Insights status list options.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/CI_AlarmsSetAlarm.png)

1. Enter a value of 50000 for **threshold** and choose **Next**\.  
![\[Console screenshot showing Contributor Insights tab and button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/CI_AlarmsSetAlarmThreashold.png)

1.  See [Using Amazon CloudWatch alarms](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html) for details on how to configure the notification for the alarm\.

## Using contributor insights \(AWS CLI\)<a name="usecontributorinsights_cli"></a>

1. Enable CloudWatch Contributor Insights for DynamoDB on the `Music` base table\.

   ```
   aws dynamodb update-contributor-insights --table-name Music --contributor-insights-action=ENABLE
   ```

1. Enable Contributor Insights for DynamoDB on the `AlbumTitle-index` global secondary index\.

   ```
   aws dynamodb update-contributor-insights --table-name Music --index-name AlbumTitle-index --contributor-insights-action=ENABLE
   ```

1. Get the status and rules for the `Music` table and all its indexes\.

   ```
   aws dynamodb describe-contributor-insights --table-name Music
   ```

1. Disable CloudWatch Contributor Insights for DynamoDB on the `AlbumTitle-index` global secondary index\.

   ```
   aws dynamodb update-contributor-insights --table-name Music --index-name AlbumTitle-index --contributor-insights-action=DISABLE
   ```

1. Get the status of the `Music` table and all its indexes\.

   ```
   aws dynamodb list-contributor-insights --table-name Music
   ```