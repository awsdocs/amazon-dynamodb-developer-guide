# Setting Up DynamoDB Local \(Downloadable Version\)<a name="DynamoDBLocal"></a>

With the downloadable version of Amazon DynamoDB, you can develop and test applications without accessing the DynamoDB web service\. Instead, the database is self\-contained on your computer\. When you're ready to deploy your application in production, you remove the local endpoint in the code, and then it points to the DynamoDB web service\.

Having this local version helps you save on throughput, data storage, and data transfer fees\. In addition, you don't need an internet connection while you develop your application\.

 DynamoDB Local is available as a [download](DynamoDBLocal.DownloadingAndRunning.md#DynamoDBLocal.DownloadingAndRunning.title) \(requires JRE\), as an [Apache Maven dependency](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DynamoDBLocal.DownloadingAndRunning.html#apache-maven), or as a [Docker image](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DynamoDBLocal.DownloadingAndRunning.html#docker)\. 

 If you prefer to use the Amazon DynamoDB web service instead, see [Setting Up DynamoDB \(Web Service\)](SettingUp.DynamoWebService.md)\. 

**Topics**
+ [Deploying DynamoDB Locally on Your Computer](DynamoDBLocal.DownloadingAndRunning.md)
+ [DynamoDB Local Usage Notes](DynamoDBLocal.UsageNotes.md)