# Step 9: Scan the Movies Table with \.NET<a name="GettingStarted.NET.09"></a>

In this step of the [Microsoft \.NET and DynamoDB Tutorial](GettingStarted.NET.md), you scan the `Movies` table in two different ways: by using a document model scan and a low\-level scan\.

**Topics**
+ [Use a Document Model Search to Scan for 1950s Movies](#GettingStarted.NET.09.a)
+ [Use a Low\-Level Scan to Retrieve 1960s Movies](#GettingStarted.NET.09.b)
+ [Next Step](#GettingStarted.NET.09.NextStep)

## Use a Document Model Search to Scan for 1950s Movies<a name="GettingStarted.NET.09.a"></a>

To set up a document model scan for 1950s movies, the `Main` function in `DynamoDB_intro` creates a `ScanOperationConfig` object with a `ScanFilter`:

```
      ScanFilter filter = new ScanFilter( );
      filter.AddCondition( "year", ScanOperator.Between, new DynamoDBEntry[ ] { 1950, 1959 } );
      ScanOperationConfig scanConfig = new ScanOperationConfig
      {
        AttributesToGet = new List<string> { "year, title, info" },
        Filter = filter
      };
```

To obtain a `Search` object for the scan, it passes the `ScanOperationConfig` object to `Table.Scan`\. Using the `Search` object, it then waits on `SearchListing_async` \(implemented in `08_Querying.cs`\) to retrieve and display the scan results\.

## Use a Low\-Level Scan to Retrieve 1960s Movies<a name="GettingStarted.NET.09.b"></a>

To set up a low\-level scan for 1960s movies, the `Main` function in `DynamoDB_intro` creates a `ScanRequest` object with various fields\.

```
      ScanRequest sRequest = new ScanRequest
      {
        TableName = "Movies",
        ExpressionAttributeNames = new Dictionary<string, string>
        {
          { "#yr", "year" }
        },
        ExpressionAttributeValues = new Dictionary<string, AttributeValue>
        {
            { ":y_a", new AttributeValue { N = "1960" } },
            { ":y_z", new AttributeValue { N = "1969" } },
        },
        FilterExpression = "#yr between :y_a and :y_z",
        ProjectionExpression = "#yr, title, info.actors[0], info.directors, info.running_time_secs"
      };
```

It then waits on the `ClientScanning_async` function implemented in the `09_Scanninging.cs` file\. `ClientScanning_async` waits in turn on the low\-level DynamoDB method `AmazonDynamoDBClient.ScanAsync` to retrieve the query results\.

**Note**  
Because "year" is a reserved word in DynamoDB, you must create an alias for it \(here `#yr`\) using `ExpressionAttributeNames` in order to use it in a low\-level expression\. 

## Next Step<a name="GettingStarted.NET.09.NextStep"></a>

[Step 10: Delete the Movies Table with \.NET](GettingStarted.NET.10.md)