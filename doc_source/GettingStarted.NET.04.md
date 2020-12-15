# Step 4: Add a Movie to the DynamoDB Table<a name="GettingStarted.NET.04"></a>

In this step of the [Microsoft \.NET and DynamoDB Tutorial](GettingStarted.NET.md), you add a new movie record to the `Movies` table in Amazon DynamoDB\. The `Main` function in `DynamoDB_intro` starts by creating a DynamoDB document model `Document` and then waits on `WritingNewMovie_async`, which is implemented in the `04_WritingNewItem.cs` file\.

```
using System;
using System.Threading.Tasks;
using Amazon.DynamoDBv2.DocumentModel;

namespace DynamoDB_intro
{
    public static partial class DdbIntro
    {
        public static async Task<bool> CheckingForMovie_async(Document newItem)
        {
            int year = (int)newItem["year"];
            string name = newItem["title"];

            var response = await ReadingMovie_async(year, name);

            return response.Count > 0;
        }

        public static async Task<bool> WritingNewMovie_async(Document newItem)
        {
            var result = false;

            try
            {
                var writeNew = await MoviesTable.PutItemAsync(newItem);
                Console.WriteLine("  -- Writing a new movie to the Movies table...");

                Console.WriteLine("      -- Wrote the item successfully!");
                result = true;
            }
            catch (Exception ex)
            {
                Console.WriteLine("      FAILED to write the new movie, because:\n       {0}.", ex.Message);
            }

            return result;
        }
    }
```

`WritingNewMovie_async` begins by checking to determine whether the new movie has already been added to the `Movies` table\. If it has not, it waits for the DynamoDB `Table.PutItemAsyn` method to add the new movie record\.

## For More Information<a name="GettingStarted.NET.04.info"></a>
+ To learn more about reading and writing data in DynamoDB tables, see [Working with Items and Attributes](WorkingWithItems.md)\.
+ For more information about the DynamoDB document model API, see [\.NET: Document Model](DotNetSDKMidLevel.md)\.
+ For more information about asynchronous methods, see [AWS Asynchronous APIs for \.NET](https://docs.aws.amazon.com/sdk-for-net/v3/developer-guide/sdk-net-async-api.html)\.

## Next Step<a name="GettingStarted.NET.04.NextStep"></a>

[Step 5: Read and Display a Record from the DynamoDB Table](GettingStarted.NET.05.md)