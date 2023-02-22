# Connecting to live datasets<a name="workbench.querybuilder.connect"></a>

To connect to your Amazon DynamoDB tables with NoSQL Workbench, you must first connect to your AWS account\.

**To add a connection to your database**

1. In NoSQL Workbench, in the navigation pane on the left side, choose the **Operation builder** icon\.  
![\[Console screenshot showing the operation builder icon.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/QueryBuilderChoose.png)

1. Choose **Add connection**\.  
![\[Console screenshot showing the add connection button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/QueryBuilderConnectToDynamoDBButton.png)

1. Specify the following information:
   + **Account alias**
   + **AWS Region**
   + **Access key ID**
   + **Secret access key**

   For more information about how to obtain the access keys, see [Getting an AWS access key](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/SettingUp.DynamoWebService.html#SettingUp.DynamoWebService.GetCredentials)\.

   You can optionally, specify the following:
   + [https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_use-resources.html](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_use-resources.html)
   + [https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_identifiers.html#identifiers-arns](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_identifiers.html#identifiers-arns)

1. Choose **Connect**\.  
![\[Console screenshot showing the connect button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/QueryBuilderConnect.png)

    If you don't want to sign up for a free tier account, and prefer to use [ DynamoDB local \(downloadable version\)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DynamoDBLocal.html): 

   1. Choose the **Local** tab on the connection screen\.

   1. Specify the following information:
      + **Connection name**
      + **Port**

   1. Choose the **connect** button\.  
![\[Console screenshot showing the Create table button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/QueryBuilderConnectLocal.png)

1. On the created connection, choose **Open**\.  
![\[Console screenshot showing the open button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/QueryBuilderOpenConnection.png)

After connecting to your DynamoDB database, the list of available tables appears in the left pane\. Choose one of the tables to return a sample of the data stored in the table\.

You can now run queries against the selected table\.

**To run queries on a table**

1. In the **Attribute name** list, choose the attribute that you want to query on\.

1. Specify the comparison operator\.

1. Specify the data type of the value\.

1. Specify the value to query for\.

1. Choose **Scan**\.

For more information about this operation, see [Scan](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Scan.html) in the *Amazon DynamoDB API Reference*\.