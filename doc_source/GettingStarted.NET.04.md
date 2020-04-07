# Step 4: Add a Movie to the DynamoDB Table<a name="GettingStarted.NET.04"></a>

In this step of the [Microsoft \.NET and DynamoDB Tutorial](GettingStarted.NET.md), you add a new movie record to the `Movies` table in Amazon DynamoDB\. The `Main` function in `DynamoDB_intro` starts by creating a DynamoDB document model `Document` and then waits on `WritingNewMovie_async`, which is implemented in the `04_WritingNewItem.cs` file\.

```
/**
 * Copyright 2010-2019 Amazon.com, Inc. or its affiliates. All Rights Reserved.
 *
 * This file is licensed under the Apache License, Version 2.0 (the "License").
 * You may not use this file except in compliance with the License. A copy of
 * the License is located at
 *
 * http://aws.amazon.com/apache2.0/
 *
 * This file is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR
 * CONDITIONS OF ANY KIND, either express or implied. See the License for the
 * specific language governing permissions and limitations under the License.
*/
using System;
using System.Threading.Tasks;
using Amazon.DynamoDBv2.DocumentModel;

namespace DynamoDB_intro
{
  public static partial class Ddb_Intro
  {
    /*--------------------------------------------------------------------------
     *     WritingNewMovie
     *--------------------------------------------------------------------------*/
    public static async Task WritingNewMovie_async( Document newItem )
    {
      operationSucceeded = false;
      operationFailed = false;

      int year = (int) newItem["year"];
      string name = newItem["title"];

      if( await ReadingMovie_async( year, name, false ) )
        Console.WriteLine( "  The {0} movie \"{1}\" is already in the Movies table...\n" +
                           "  -- No need to add it again... its info is as follows:\n{2}",
                           year, name, movie_record.ToJsonPretty( ) );
      else
      {
        try
        {
          Task<Document> writeNew = moviesTable.PutItemAsync(newItem, token);
          Console.WriteLine("  -- Writing a new movie to the Movies table...");
          await writeNew;
          Console.WriteLine("      -- Wrote the item successfully!");
          operationSucceeded = true;
        }
        catch (Exception ex)
        {
          Console.WriteLine("      FAILED to write the new movie, because:\n       {0}.", ex.Message);
          operationFailed = true;
        }
      }
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