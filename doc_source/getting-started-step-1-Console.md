# AWS Management Console<a name="getting-started-step-1-Console"></a>

To create a new `Music` table using the DynamoDB console:

1. Sign in to the AWS Management Console and open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. In the navigation pane on the left side of the console, choose **Dashboard**\. 

1. On the right side of the console, choose **Create Table**\.  
![\[Console screenshot showing the Create table button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/GettingStarted/CreateTableDashboard.png)

1. Enter the table details as follows:

   1. For the table name, enter **Music**\. 

   1. For the partition key, enter **Artist**\.

   1. Choose **Add sort key**\.

   1. Enter **SongTitle** as the sort key\. 

1. Choose **Create** to create the table\.  
![\[Console screenshot showing the Create table page with fields completed.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/GettingStarted/CreateTableMusic.png)