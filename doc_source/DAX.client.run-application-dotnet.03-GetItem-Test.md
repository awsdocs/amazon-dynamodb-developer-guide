# 03\-GetItem\-Test\.cs<a name="DAX.client.run-application-dotnet.03-GetItem-Test"></a>

The `03-GetItem-Test.cs` program performs `GetItem` operations on *TryDaxTable*\.

```
using Amazon.Runtime;
using Amazon.DAX;
using Amazon.DynamoDBv2.Model;
using System.Collections.Generic;
using System;
using Amazon.DynamoDBv2;
using Amazon;

namespace ClientTest
{
    class Program
    {
        static void Main(string[] args)
        {

            String hostName = args[0].Split(':')[0];
            int port = Int32.Parse(args[0].Split(':')[1]);
            Console.WriteLine("Using DAX client - hostname=" + hostName + ", port=" + port);

            var clientConfig = new DaxClientConfig(hostName, port)
            {
                AwsCredentials = FallbackCredentialsFactory.GetCredentials()
                   
            };
            var client = new ClusterDaxClient(clientConfig);

            var tableName = "TryDaxTable";

            var pk = 1;
            var sk = 10;
            var iterations = 5;

            var startTime = DateTime.Now;

            for (var i = 0; i < iterations; i++)
            {

                for (var ipk = 1; ipk <= pk; ipk++)
                {
                    for (var isk = 1; isk <= sk; isk++)
                    {
                        var request = new GetItemRequest()
                        {
                            TableName = tableName,
                            Key = new Dictionary<string, AttributeValue>() {
                            {"pk", new AttributeValue {N = ipk.ToString()} },
                            {"sk", new AttributeValue {N = isk.ToString() } }
                        }
                        };
                        var response = client.GetItemAsync(request).Result;
                        Console.WriteLine("GetItem succeeded for pk: " + ipk + ", sk: " + isk);
                    }
                }

            }

            var endTime = DateTime.Now;
            TimeSpan timeSpan = endTime - startTime;
            Console.WriteLine("Total time: " + (int)timeSpan.TotalMilliseconds + " milliseconds");

            Console.WriteLine("Hit <enter> to continue...");
            Console.ReadLine();
        }
    }
}
```