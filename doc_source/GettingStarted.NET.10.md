# Step 10: Delete the Movies Table with \.NET<a name="GettingStarted.NET.10"></a>

In this step of the [Microsoft \.NET and DynamoDB Tutorial](GettingStarted.NET.md), you delete the `Movies` table in Amazon DynamoDB\.

The `Main` function in `DynamoDB_intro` waits on `DeletingTable_async`, which is implemented in the `10_DeletingTable.cs` file\.

```
using System;
using System.Threading.Tasks;

namespace DynamoDB_intro
{
  public static partial class DdbIntro
  {
    public static async Task<bool> DeletingTable_async(string tableName)
    {
      var tblDelete = await Client.DeleteTableAsync(tableName);
      return true;
    }
  }
```

`DeletingTable_async` waits on the low\-level DynamoDB method `AmazonDynamoDBClient.DeleteTableAsync` to delete the table\.

## For More Information<a name="GettingStarted.NET.10.info"></a>
+ To learn more about reading and writing data in DynamoDB tables, see [Working with Items and Attributes](WorkingWithItems.md)\.
+ For more information about the DynamoDB document model API, see [\.NET: Document Model](DotNetSDKMidLevel.md)\.
+ For more information about asynchronous methods, see [AWS Asynchronous APIs for \.NET](https://docs.aws.amazon.com/sdk-for-net/v3/developer-guide/sdk-net-async-api.html)\.