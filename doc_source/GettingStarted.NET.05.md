# Step 5: Read and Display a Record from the DynamoDB Table<a name="GettingStarted.NET.05"></a>

In this step of the [Microsoft \.NET and DynamoDB Tutorial](GettingStarted.NET.md), you retrieve and display the new movie record that you added in [Step 4](GettingStarted.NET.04.md)\. The `Main` function in `DynamoDB_intro` does this by waiting on `ReadingMovie_async`, which is implemented in the `05_ReadingItem.cs` file\.

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
     *                             ReadingMovie_async
     *--------------------------------------------------------------------------*/
    public static async Task<bool> ReadingMovie_async( int year, string title, bool report )
    {
      // Create Primitives for the HASH and RANGE portions of the primary key
      Primitive hash = new Primitive(year.ToString(), true);
      Primitive range = new Primitive(title, false);

      operationSucceeded = false;
      operationFailed = false;
      try
      {
        Task<Document> readMovie = moviesTable.GetItemAsync(hash, range, token);
        if( report )
          Console.WriteLine( "  -- Reading the {0} movie \"{1}\" from the Movies table...", year, title );
        movie_record = await readMovie;
        if( movie_record == null )
        {
          if( report )
            Console.WriteLine( "     -- Sorry, that movie isn't in the Movies table." );
          return ( false );
        }
        else
        {
          if( report )
            Console.WriteLine( "     -- Found it!  The movie record looks like this:\n" +
                                movie_record.ToJsonPretty( ) );
          operationSucceeded = true;
          return ( true );
        }
      }
      catch( Exception ex )
      {
        Console.WriteLine( "     FAILED to get the movie, because: {0}.", ex.Message );
        operationFailed = true;
      }
      return ( false );
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