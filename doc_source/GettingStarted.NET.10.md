# Step 10: Delete the Movies Table with \.NET<a name="GettingStarted.NET.10"></a>

In this step of the [Microsoft \.NET and DynamoDB Tutorial](GettingStarted.NET.md), you delete the `Movies` table in Amazon DynamoDB\.

The `Main` function in `DynamoDB_intro` waits on `DeletingTable_async`, which is implemented in the `10_DeletingTable.cs` file\.

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

namespace DynamoDB_intro
{
  public static partial class Ddb_Intro
  {
    /*--------------------------------------------------------------------------
     *                DeletingTable_async
     *--------------------------------------------------------------------------*/
    public static async Task<bool> DeletingTable_async( string tableName )
    {
      operationSucceeded = false;
      operationFailed = false;

      Console.WriteLine( "  -- Trying to delete the table named \"{0}\"...", tableName );
      pause( );
      Task tblDelete = client.DeleteTableAsync( tableName );
      try
      {
        await tblDelete;
      }
      catch( Exception ex )
      {
        Console.WriteLine( "     ERROR: Failed to delete the table, because:\n            " + ex.Message );
        operationFailed = true;
        return ( false );
      }
      Console.WriteLine( "     -- Successfully deleted the table!" );
      operationSucceeded = true;
      pause( );
      return ( true );
    }
  }
```

`DeletingTable_async` waits on the low\-level DynamoDB method `AmazonDynamoDBClient.DeleteTableAsync` to delete the table\.

## For More Information<a name="GettingStarted.NET.10.info"></a>
+ To learn more about reading and writing data in DynamoDB tables, see [Working with Items and Attributes](WorkingWithItems.md)\.
+ For more information about the DynamoDB document model API, see [\.NET: Document Model](DotNetSDKMidLevel.md)\.
+ For more information about asynchronous methods, see [AWS Asynchronous APIs for \.NET](https://docs.aws.amazon.com/sdk-for-net/v3/developer-guide/sdk-net-async-api.html)\.