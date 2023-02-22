# Committing a data model to DynamoDB<a name="workbench.Visualizer.Commit"></a>

 When you are satisfied with your data model, you can commit the model to Amazon DynamoDB\. 

**Note**  
This action results in the creation of server\-side resources in AWS for the tables and global secondary indexes represented in the data model\.
Tables are created with the following characteristics:  
Auto scaling is set to 70 percent target utilization\.
Provisioned capacity is set to 5 read capacity units and 5 write capacity units\. 
Global secondary indexes are created with provisioned capacity of 10 read capacity units and 5 write capacity units\.

**To commit the data model to DynamoDB**

1. In the navigation pane on the left side, choose the **visualizer** icon\.  
![\[Console screenshot showing the visualizer icon in DynamoDB.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/VisualizerChoose.png)

1. Choose **Commit to DynamoDB**\.  
![\[Console screenshot showing the commit to DynamoDB button.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/VisualizerCommitToDynamoDB.png)

1. Choose an already existing connection, or create a new connection by choosing the **Add new remote connection** tab\. 
   + To add a new connection, specify the following information:
     + **Account Alias**
     + **AWS Region**
     + **Access key ID**
     + **Secret access key**

     For more information about how to obtain the access keys, see [Getting an AWS access key](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/SettingUp.DynamoWebService.html#SettingUp.DynamoWebService.GetCredentials)\.
   + You can optionally specify the following:
     + [https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_use-resources.html](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_use-resources.html)
     + [https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_identifiers.html#identifiers-arns](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_identifiers.html#identifiers-arns)
   + If you don't want to sign up for a free tier account, and prefer to use [DynamoDB local \(downloadable version\)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DynamoDBLocal.html):

     1. Choose the **Add a new DynamoDB local connection** tab\.

     1. Specify the **Connection name** and **Port**\.

1. Choose **Commit**\.  
![\[Console screenshot showing the add new connection screen.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/VisualizerCommitToDynamoDBDetails.png)

**Note**  
 If you installed DynamoDB local as part of the NoSQL Workbench setup, you'll need to turn DynamoDB local on by using the **DynamoDB local Server** toggle at the bottom left of the NoSQL Workbench screen\. See [Install NoSQL Workbench for DynamoDB](workbench.settingup.install.md) for more information on this toggle\. 