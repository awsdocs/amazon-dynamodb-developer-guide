# Step 2: Create a DynamoDB Table Using the Low\-Level API<a name="GettingStarted.NET.02"></a>

The document model in the AWS SDK for \.NET doesn't provide for creating tables, so you have to use the low\-level APIs\. For more information, see [Working with DynamoDB Tables in \.NET](LowLevelDotNetWorkingWithTables.md)\.

In this step of the [Microsoft \.NET and DynamoDB Tutorial](GettingStarted.NET.md), you create a table named `Movies` in Amazon DynamoDB\. The primary key for the table is composed of the following attributes:
+ `year` – The partition key\. The `AttributeType` is `N` for number\.
**Note**  
Because "year" is a reserved word in DynamoDB, you need to create an alias for it \(such as `#yr`\) using an `ExpressionAttributeNames` object when referring to it in a low\-level expression\. 
+ `title` – The sort key\. The `AttributeType` is `S` for string\.

The `Main` function in `DynamoDB_intro` does this by waiting on an asynchronous `CreatingTable_async` function implemented in the `02_CreatingTable.cs` file\.

```
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Amazon.DynamoDBv2.Model;

namespace DynamoDB_intro
{
    public static partial class DdbIntro
    {
        public static async Task<bool> CheckingTableExistence_async(string tblNm)
        {
            var response = await DdbIntro.Client.ListTablesAsync();
            return response.TableNames.Contains(tblNm);
        }

        public static async Task<bool> CreateTable_async(string tableName,
            List<AttributeDefinition> tableAttributes,
            List<KeySchemaElement> tableKeySchema,
            ProvisionedThroughput provisionedThroughput)
        {
            bool response = true;

            // Build the 'CreateTableRequest' structure for the new table
            var request = new CreateTableRequest
            {
                TableName = tableName,
                AttributeDefinitions = tableAttributes,
                KeySchema = tableKeySchema,
                // Provisioned-throughput settings are always required,
                // although the local test version of DynamoDB ignores them.
                ProvisionedThroughput = provisionedThroughput
            };

            try
            {
                var makeTbl = await DdbIntro.Client.CreateTableAsync(request);
            }
            catch (Exception)
            {
                response = false;
            }

            return response;
        }

        public static async Task<TableDescription> GetTableDescription(string tableName)
        {
            TableDescription result = null;

            // If the table exists, get its description.
            try
            {
                var response = await DdbIntro.Client.DescribeTableAsync(tableName);
                result = response.Table;
            }
            catch (Exception)
            {}

            return result;
        }
    }
}
```

The `CreatingTable_async` function starts by waiting on an asynchronous `checkingTableExistence_async` function to determine whether a table named "Movies" already exists\. If the table exists, `checkingTableExistence_async` retrieves the `TableDescription` for the existing `Table`\.

If the table doesn't already exist, `CreatingTable_async` waits on `CreateNewTable_async` to create the `Movies` table using the DynamoDB client API `CreateTableAsync`\.

The `DynamoDB_intro` sample uses asynchronous methods rather than synchronous methods wherever possible\. This is because \.NET core supports only asynchronous methods, and the asynchronous model is generally preferable when performance is crucial\. For more information, see [AWS Asynchronous APIs for \.NET](https://docs.aws.amazon.com/sdk-for-net/v3/developer-guide/sdk-net-async-api.html)\.

To learn more about managing tables, see [Working with Tables and Data in DynamoDB](WorkingWithTables.md)\.

## Next Step<a name="GettingStarted.NET.02.NextStep"></a>

[Step 3: Load Sample Data into the DynamoDB Table](GettingStarted.NET.03.md)