# Step 5: Read and Display a Record from the DynamoDB Table<a name="GettingStarted.NET.05"></a>

In this step of the [Microsoft \.NET and DynamoDB Tutorial](GettingStarted.NET.md), you retrieve and display the new movie record that you added in [Step 4](GettingStarted.NET.04.md)\. The `Main` function in `DynamoDB_intro` does this by waiting on `ReadingMovie_async`, which is implemented in the `05_ReadingItem.cs` file\.

```
using System;
using System.Threading.Tasks;
using Amazon.DynamoDBv2.DocumentModel;

namespace DynamoDB_intro
{
  public static partial class DdbIntro
  {
      public static async Task<Document> ReadingMovie_async(int year, string title)
    {
      // Create Primitives for the HASH and RANGE portions of the primary key
      Primitive hash = new Primitive(year.ToString(), true);
      Primitive range = new Primitive(title, false);

      try
      {
        var movieItem = await MoviesTable.GetItemAsync(hash, range, Token);
        return movieItem;
      }
      catch (Exception)
      {
          return null;
      }
    }
  }
```

`ReadingMovie_async` in turn waits on the DynamoDB `Table.GetItemAsyn` method to retrieve the new movie record as a `Document`\. `ReadingMovie_async` then displays the movie as JSON text using the `Document.ToJsonPretty` method\.

## For More Information<a name="GettingStarted.NET.05.info"></a>
+ To learn more about reading and writing data in DynamoDB tables, see [Working with Items and Attributes](WorkingWithItems.md)\.
+ For more information about the DynamoDB document model API, see [\.NET: Document Model](DotNetSDKMidLevel.md)\.
+ For more information about asynchronous methods, see [AWS Asynchronous APIs for \.NET](https://docs.aws.amazon.com/sdk-for-net/v3/developer-guide/sdk-net-async-api.html)\.

## Next Step<a name="GettingStarted.NET.05.NextStep"></a>

[Step 6: Update the New Movie Record in the DynamoDB Table](GettingStarted.NET.06.md)