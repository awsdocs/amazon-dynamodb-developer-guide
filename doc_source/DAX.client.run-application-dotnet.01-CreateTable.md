# 01\-CreateTable\.cs<a name="DAX.client.run-application-dotnet.01-CreateTable"></a>

The `01-CreateTable.cs` program creates a table \(*TryDaxTable*\)\. The remaining \.NET programs in this section depend on this table\.

```
using Amazon.DynamoDBv2.Model;
using System.Collections.Generic;
using System;
using Amazon.DynamoDBv2;

namespace ClientTest
{
    class Program
    {
        static void Main(string[] args)
        {

            AmazonDynamoDBClient client = new AmazonDynamoDBClient();

            var tableName = "TryDaxTable";

            var request = new CreateTableRequest()
            {
                TableName = tableName,
                KeySchema = new List<KeySchemaElement>()
                {
                    new KeySchemaElement{ AttributeName = "pk",KeyType = "HASH"},
                    new KeySchemaElement{ AttributeName = "sk",KeyType = "RANGE"}
                },
                AttributeDefinitions = new List<AttributeDefinition>() {
                    new AttributeDefinition{ AttributeName = "pk",AttributeType = "N"},
                    new AttributeDefinition{ AttributeName = "sk",AttributeType  = "N"}
                },
                ProvisionedThroughput = new ProvisionedThroughput()
                {
                    ReadCapacityUnits = 10,
                    WriteCapacityUnits = 10
                }
            };

            var response = client.CreateTableAsync(request).Result;

            Console.WriteLine("Hit <enter> to continue...");
            Console.ReadLine();
        }
    }
}
```