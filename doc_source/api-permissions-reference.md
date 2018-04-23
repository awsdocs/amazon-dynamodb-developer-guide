# DynamoDB API Permissions: Actions, Resources, and Conditions Reference<a name="api-permissions-reference"></a>

When you are setting up [Access Control](authentication-and-access-control.md#access-control) and writing a permissions policy that you can attach to an IAM identity \(identity\-based policies\), you can use the following table as a reference\. The table lists each DynamoDB API operation, the corresponding actions for which you can grant permissions to perform the action, and the AWS resource for which you can grant the permissions\. You specify the actions in the policy's `Action` field, and you specify the resource value in the policy's `Resource` field\. 

You can use AWS\-wide condition keys in your DynamoDB policies to express conditions\. For a complete list of AWS\-wide keys, see [Available Keys](http://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#AvailableKeys) in the *IAM User Guide*\. 

In addition to the AWS\-wide condition keys, DynamoDB has its own specific keys that you can use in conditions\. For more information, see [Using IAM Policy Conditions for Fine\-Grained Access Control](specifying-conditions.md)\.

**Note**  
To specify an action, use the `dynamodb:` prefix followed by the API operation name \(for example, `dynamodb:CreateTable`\)\.

If you see an expand arrow \(**↗**\) in the upper\-right corner of the table, you can open the table in a new window\. To close the window, choose the close button \(**X**\) in the lower\-right corner\.


**Amazon DynamoDB API and Required Permissions for Actions**  

| DynamoDB API Operations | Required Permissions \(API Actions\) | Resources | 
| --- | --- | --- | 
|  [BatchGetItem](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_BatchGetItem.html)  | dynamodb:BatchGetItem |  arn:aws:dynamodb:region:account\-id:table/table\-name or arn:aws:dynamodb:region:account\-id:table/\*  | 
|  [BatchWriteItem](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_BatchWriteItem.html)  |  dynamodb:BatchWriteItem  |  arn:aws:dynamodb:region:account\-id:table/table\-name or arn:aws:dynamodb:region:account\-id:table/\*  | 
|  [CreateTable](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_CreateTable.html)  | dynamodb:CreateTable |  arn:aws:dynamodb:region:account\-id:table/table\-name or arn:aws:dynamodb:region:account\-id:table/\*  | 
| [DeleteItem](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DeleteItem.html) | dynamodb:DeleteItem |  arn:aws:dynamodb:region:account\-id:table/table\-name or arn:aws:dynamodb:region:account\-id:table/\*  | 
| [DeleteTable](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DeleteTable.html)  |  dynamodb:DeleteTable  |  arn:aws:dynamodb:region:account\-id:table/table\-name or arn:aws:dynamodb:region:account\-id:table/\*  | 
| [DescribeLimits](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DescribeLimits.html) | dynamodb:DescribeLimits | arn:aws:dynamodb:region:account\-id:\* | 
| DescribeReservedCapacity | dynamodb:DescribeReservedCapacity | arn:aws:dynamodb:region:account\-id:\* | 
| DescribeReservedCapacityOfferings | dynamodb:DescribeReservedCapacityOfferings | arn:aws:dynamodb:region:account\-id:\* | 
|  [DescribeStream](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_streams_DescribeStream.html)  | dynamodb:DescribeStream |  arn:aws:dynamodb:region:account\-id:table/table\-name/stream/stream\-label or arn:aws:dynamodb:region:account\-id:table/table\-name/stream/\*  | 
| [DescribeTable](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DescribeTable.html) | dynamodb:DescribeTable |  arn:aws:dynamodb:region:account\-id:table/table\-name or arn:aws:dynamodb:region:account\-id:table/\*  | 
| [DescribeTimeToLive](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DescribeTimeToLive.html) | dynamodb:DescribeTimeToLive |  arn:aws:dynamodb:region:account\-id:table/table\-name or arn:aws:dynamodb:region:account\-id:table/\*  | 
| [GetItem](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_GetItem.html) | dynamodb:GetItem |  arn:aws:dynamodb:region:account\-id:table/table\-name or arn:aws:dynamodb:region:account\-id:table/\*  | 
|  [GetRecords](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_streams_GetRecords.html)  | dynamodb:GetRecords |  arn:aws:dynamodb:region:account\-id:table/table\-name/stream/stream\-label or arn:aws:dynamodb:region:account\-id:table/table\-name/stream/\*  | 
|  [GetShardIterator](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_streams_GetShardIterator.html)  | dynamodb:GetShardIterator | arn:aws:dynamodb:region:account\-id:table/table\-name/stream/stream\-label orarn:aws:dynamodb:region:account\-id:table/table\-name/stream/\* | 
|  [ListStreams](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_streams_ListStreams.html)  | dynamodb:ListStreams |  arn:aws:dynamodb:region:account\-id:table/table\-name/stream/\* or arn:aws:dynamodb:region:account\-id:table/\*/stream/\*  | 
| [ListTables](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_ListTables.html) | dynamodb:ListTables | \*  | 
| [ListTagsOfResource](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_ListTagsOfResource.html) | dynamodb:ListTagsOfResource |  arn:aws:dynamodb:region:account\-id:table/table\-name or arn:aws:dynamodb:region:account\-id:table/\*  | 
| PurchaseReservedCapacityOfferings | dynamodb:PurchaseReservedCapacityOfferings | arn:aws:dynamodb:region:account\-id:\* | 
| [PutItem](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_PutItem.html) | dynamodb:PutItem |  arn:aws:dynamodb:region:account\-id:table/table\-name or arn:aws:dynamodb:region:account\-id:table/\*  | 
| [Query](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Query.html) | dynamodb:Query | To query a table:arn:aws:dynamodb:region:account\-id:table/table\-nameor:arn:aws:dynamodb:region:account\-id:table/\*To query an index:arn:aws:dynamodb:region:account\-id:table/table\-name/index/index\-nameor:arn:aws:dynamodb:region:account\-id:table/table\-name/index/\* | 
| [Scan](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Scan.html) |  dynamodb:Scan  | To scan a table:arn:aws:dynamodb:region:account\-id:table/table\-nameor:arn:aws:dynamodb:region:account\-id:table/\*To scan an index:arn:aws:dynamodb:region:account\-id:table/table\-name/index/index\-nameor:arn:aws:dynamodb:region:account\-id:table/table\-name/index/\* | 
| [TagResource](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_TagResource.html) |  dynamodb:TagResource  |  arn:aws:dynamodb:region:account\-id:table/table\-name or arn:aws:dynamodb:region:account\-id:table/\*  | 
| [UpdateItem](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_UpdateItem.html) |  dynamodb:UpdateItem  |  arn:aws:dynamodb:region:account\-id:table/table\-name or arn:aws:dynamodb:region:account\-id:table/\*  | 
| [UpdateTable](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_UpdateTable.html) |  dynamodb:UpdateTable  |  arn:aws:dynamodb:region:account\-id:table/table\-name or arn:aws:dynamodb:region:account\-id:table/\*  | 
| [UpdateTimeToLive](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_UpdateTimeToLive.html) |  dynamodb:UpdateTimeToLive  |  arn:aws:dynamodb:region:account\-id:table/table\-name or arn:aws:dynamodb:region:account\-id:table/\*  | 
| [UntagResource](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_UntagResource.html) |  dynamodb:UntagResource  |  arn:aws:dynamodb:region:account\-id:table/table\-name or arn:aws:dynamodb:region:account\-id:table/\*  | 

## Related Topics<a name="w3ab1c29c15c23"></a>
+ [Access Control](authentication-and-access-control.md#access-control)
+ [Using IAM Policy Conditions for Fine\-Grained Access Control](specifying-conditions.md)