# Step 6: Update the New Movie Record in the DynamoDB Table<a name="GettingStarted.NET.06"></a>

In this step of the [Microsoft \.NET and DynamoDB Tutorial](GettingStarted.NET.md), you update the new movie record in several different ways\.

**Topics**
+ [Change Plot and Rating, and Add Actors](#GettingStarted.NET.06.a)
+ [Increment the Movie Rating Atomically](#GettingStarted.NET.06.b)
+ [Try to Update Using a Condition That Fails](#GettingStarted.NET.06.c)
+ [For More Information](#GettingStarted.NET.06.d)
+ [Next Step](#GettingStarted.NET.06.NextStep)

## Change Plot and Rating, and Add Actors<a name="GettingStarted.NET.06.a"></a>

The `Main` function in `DynamoDB_intro` changes the plot and rating of the movie record that you added in [Step 4](GettingStarted.NET.04.md), and it also adds a list of actors to it\. The document model in the AWS SDK for \.NET doesn't support updating nested attributes such as the items under the `info` attribute\. Because of this, `Main` uses the low\-level client API rather than a document\-model `Table` method\.

It starts by creating a low\-level `UpdateItemRequest` to make this change\.

```
      UpdateItemRequest updateRequest = new UpdateItemRequest()
        {
          TableName = movies_table_name,
          Key = new Dictionary<string, AttributeValue>
          {
            { partition_key_name, new AttributeValue { N = "2018" } },
            { sort_key_name, new AttributeValue { S = "The Big New Movie" } }
          },
          ExpressionAttributeValues = new Dictionary<string, AttributeValue>
          {
            { ":r", new AttributeValue { N = "5.5" } },
            { ":p", new AttributeValue { S = "Everything happens all at once!" } },
            { ":a", new AttributeValue { L = new List<AttributeValue>
              { new AttributeValue { S ="Larry" },
                new AttributeValue { S = "Moe" },
                new AttributeValue { S = "Curly" } }
              }
            }
          },
          UpdateExpression = "SET info.rating = :r, info.plot = :p, info.actors = :a",
          ReturnValues = "NONE"
        };
```

Setting `ReturnValues` to `NONE` specifies that no update information should be returned\. However, when `Main` then waits on `UpdatingMovie_async`, it sets the *report* parameter to `true`\. This causes `UpdatingMovie_async` to change `ReturnValues` to `ALL_NEW`, meaning that the updated item should be returned in its entirety\. 

The `UpdatingMovie_async` is implemented in the `06_UpdatingItem.cs` file\.

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
using System.Collections.Generic;
using System.Text;
using Amazon.DynamoDBv2.Model;

namespace DynamoDB_intro
{
  public static partial class Ddb_Intro
  {
    /*--------------------------------------------------------------------------
     *                             UpdatingMovie_async
     *--------------------------------------------------------------------------*/
    public static async Task<bool> UpdatingMovie_async( UpdateItemRequest updateRequest, bool report )
    {
      UpdateItemResponse updateResponse = null;

      operationSucceeded = false;
      operationFailed = false;
      if( report )
      {
        Console.WriteLine( "  -- Trying to update a movie item..." );
        updateRequest.ReturnValues = "ALL_NEW";
      }

      try
      {
        updateResponse = await client.UpdateItemAsync( updateRequest );
        Console.WriteLine( "     -- SUCCEEDED in updating the movie item!" );
      }
      catch( Exception ex )
      {
        Console.WriteLine( "     -- FAILED to update the movie item, because:\n       {0}.", ex.Message );
        if( updateResponse != null )
          Console.WriteLine( "     -- The status code was " + updateResponse.HttpStatusCode.ToString( ) );
        operationFailed = true;return ( false );
      }
      if( report )
      {
        Console.WriteLine( "     Here is the updated movie informtion:" );
        Console.WriteLine( movieAttributesToJson( updateResponse.Attributes ) );
      }
      operationSucceeded = true;
      return ( true );
    }
  }
}
```

`UpdatingMovie_async` waits on the low\-level DynamoDB `Client.UpdateItemAsync` method to update the movie record\. If the update is successful, and if the *report* parameter is `true`, `UpdatingMovie_async` displays the updated movie record\.

Where the document model has a handy `Document.ToJsonPretty( )` method for displaying document content, working with low\-level attribute values is a little more complicated\. The `00b_DDB_Attributes.cs` file can provide some examples of how to access and work with `AttributeValue` objects\.

## Increment the Movie Rating Atomically<a name="GettingStarted.NET.06.b"></a>

DynamoDB supports the atomic update of counters, where you use a low\-level update method to increment or decrement the value of an existing attribute without interference from other write requests\. \(All write requests in DynamoDB are applied in the order in which they are received\.\)

To increment the rating value in the movie that you just created, the `Main` function makes the following changes in the `UpdateItemRequest` that it used in the previous update\.

```
      updateRequest.ExpressionAttributeValues = new Dictionary<string, AttributeValue>
      {
        { ":inc", new AttributeValue { N = "1" } }
      };
      updateRequest.UpdateExpression = "SET info.rating = info.rating + :inc";
```

Then, once again, it waits on `UpdatingMovie_async` to make the change\.

## Try to Update Using a Condition That Fails<a name="GettingStarted.NET.06.c"></a>

You can also add a condition to an update request, so that if the condition is not met, the update does not occur\.

To demonstrate this, the `Main` function makes the following changes to the `UpdateItemRequest` that it just used to increment the movie rating\.

```
      updateRequest.ExpressionAttributeValues.Add( ":n", new AttributeValue { N = "3" } );
      updateRequest.ConditionExpression = "size(info.actors) > :n";
```

The update can now occur only if there are more than three actors in the movie record being updated\. Because there are only three actors listed, the condition fails when `Main` waits on `UpdatingMovie_async`, and the update does not occur\.

## For More Information<a name="GettingStarted.NET.06.d"></a>
+ To learn more about reading and writing data in DynamoDB tables, see [Working with Items and Attributes](WorkingWithItems.md)\.
+ For more information about the DynamoDB document model API, see [\.NET: Document Model](DotNetSDKMidLevel.md)\.
+ For more information about asynchronous methods, see [AWS Asynchronous APIs for \.NET](https://docs.aws.amazon.com/sdk-for-net/v3/developer-guide/sdk-net-async-api.html)\.

## Next Step<a name="GettingStarted.NET.06.NextStep"></a>

[Step 7: Conditionally Delete](GettingStarted.NET.07.md)