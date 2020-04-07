# Exploring Datasets<a name="workbench.querybuilder.connect"></a>

To explore your Amazon DynamoDB tables, you first need to connect to your AWS account\.

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

   For more information about how to obtain the access keys, see [Getting an AWS Access Key](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/SettingUp.DynamoWebService.html#SettingUp.DynamoWebService.GetCredentials)\.

   You can optionally, specify the following:
   + [https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_use-resources.html](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_use-resources.html)
   + [https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_identifiers.html#identifiers-arns](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_identifiers.html#identifiers-arns)

1. Choose **Connect**\.  
![\[Console screenshot showing the connect button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/QueryBuilderConnect.png)

   If you don't want to sign up for a free tier account, and prefer to use [DynamoDB Local \(Downloadable Version\)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DynamoDBLocal.html): 

   1. Choose the **Local** tab on the connection screen

   1. Specify the following information:
      + **Connection name**
      + **Port**

   1. Choose the **connect** button\.  
![\[Console screenshot showing the Create table button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/QueryBuilderConnectLocal.png)

1. On the created connection, choose **Open**\.  
![\[Console screenshot showing the open button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/QueryBuilderOpenConnection.png)
**Note**  
When adding a connection to [DynamoDB Local \(Downloadable Version\)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DynamoDBLocal.html), NoSQL Workbench generates a random AWS access key ID and secret access key\. To retrieve the values of these keys, choose **View credentials** from the connection context menu\.   

![\[Console screenshot showing the open button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/QueryBuilderLocalConnectionkeys.png)
When adding a connection to [DynamoDB Local \(Downloadable Version\)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DynamoDBLocal.html), the NoSQL Workbench uses `localhost` as the Region for the DynamoDB Local connection\.
If [DynamoDB Local \(Downloadable Version\)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DynamoDBLocal.html) is launched without the `-sharedDb` command line option, a new database file is created in DynamoDB Local for each connection from NoSQL Workbench\.
You can't use an existing [DynamoDB Local \(Downloadable Version\)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DynamoDBLocal.html) database \(created via AWS CLI/SDK\) via NoSQL Workbench, unless the DynamoDB Local instance was started with the `-sharedDb` command line option\.

After connecting to your DynamoDB database, the list of available tables appears in the left pane\. Choose one of the tables to return a sample of the data stored in the table\.

You can now execute queries against the selected table\.

**To execute queries on a table**

1. In the **Attribute name** list, choose the attribute that you want to query on\.

1. Specify the comparison operator\.

1. Specify the data type of the value\.

1. Specify the value to query for\.

1. Choose **Scan**\.

![\[Console screenshot showing the attribute dropdown list.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/QueryBuilderScan.png)

For more information about this operation, see [Scan](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Scan.html) in the *Amazon DynamoDB API Reference*\.

You can update and delete any row that is returned\. To update, choose the **Edit** \(pencil\) icon on the right side of the row\. To delete, choose the **Delete** \(trash can\) icon\.

![\[Console screenshot showing the pencil and trash can icons.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/querybuildertrashandpencil.png)

Choosing the **Delete** icon starts a [Delete Item](workbench.querybuilder.operationbuilder.md#workbench.querybuilder.operationbuilder.Delete) operation\.

![\[Console screenshot showing the DeleteItem operation builder.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/QueryBuildertrashcanresult.png)

Choosing the **Edit** icon starts an [Update Item](workbench.querybuilder.operationbuilder.md#workbench.querybuilder.operationbuilder.update) operation\.

![\[Console screenshot showing the updateitem operation builder.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/QueryBuilderpensilresult.png)

Choosing the **\+** \(plus sign\) in the bottom left of the row list starts a [Put Item](workbench.querybuilder.operationbuilder.md#workbench.querybuilder.operationbuilder.Put) operation\.

![\[Console screenshot showing the putitem operation builder.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/QueryBuilderplusresult.png)