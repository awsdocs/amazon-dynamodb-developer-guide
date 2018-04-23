# 06\-DeleteTable\.cs<a name="DAX.client.run-application-dotnet.06-DeleteTable"></a>

The `06-DeleteTable.cs` program deletes *TryDaxTable*\. Run this program after you are done testing\.

```
using Amazon.DynamoDBv2.Model;
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

            var request = new DeleteTableRequest()
            {
                TableName = tableName
            };

            var response = client.DeleteTableAsync(request).Result;


            Console.WriteLine("Hit <enter> to continue...");
            Console.ReadLine();
        }
    }

}
```