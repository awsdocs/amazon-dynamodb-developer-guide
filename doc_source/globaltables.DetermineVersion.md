# Determining the global table version you are using<a name="globaltables.DetermineVersion"></a>

There are two versions of DynamoDB global tables available: [Version 2019\.11\.21 \(Current\)](globaltables.V2.md) and [Version 2017\.11\.29](globaltables.V1.md)\. We recommend using [Version 2019\.11\.21 \(Current\)](globaltables.V2.md) of global tables, which enables you to dynamically add new replica tables from a table populated with data\. [Version 2019\.11\.21 \(Current\)](globaltables.V2.md) is more efficient and consumes less write capacity than [Version 2017\.11\.29](globaltables.V1.md)\.

To find out which version of global tables you are using, do the following:

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/home](https://console.aws.amazon.com/dynamodb/home)\.

1. In the navigation pane on the left side of the console, choose **Tables**\.

1. Choose the table you want to use\.

1. Choose the **Global Tables** tab\.

1. The **Global table version** displays the version of global tables in use\.

![\[\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/GlobalTables-determineversion.png)

**Note**  
If you are using [Version 2019\.11\.21 \(Current\)](globaltables.V2.md) of global tables and you also use the [Time to Live](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/TTL.html) feature, DynamoDB replicates TTL deletes to all replica tables\. The initial TTL delete does not consume write capacity in the region in which the TTL expiry occurs\. However, the replicated TTL delete to the replica table\(s\) consumes a replicated write capacity unit when using provisioned capacity, or replicated write when using on\-demand capacity mode, in each of the replica regions and applicable charges will apply\.
In [Version 2019\.11\.21 \(Current\)](globaltables.V2.md), when a TTL delete occurs it is replicated to all replica regions\. These replicated writes do not contain `type` or `principalID` properties\. This can make it difficult to distinguish a TTL delete from a user delete in the replicated tables\.