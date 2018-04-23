# 05\-Scan\-Test\.cs<a name="DAX.client.run-application-dotnet.05-Scan-Test"></a>

The `05-Scan-Test.cs` program performs `Scan` operations on *TryDaxTable*\.

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
                AwsCredentials = FallbackCredentialsFactory.GetCredentials(

            };
            var client = new ClusterDaxClient(clientConfig);

            var tableName = "TryDaxTable";

            var iterations = 5;

            var startTime = DateTime.Now;

            for (var i = 0; i < iterations; i++)
            {
                var request = new ScanRequest()
                {
                    TableName = tableName
                };
                var response = client.ScanAsync(request).Result;
                Console.WriteLine(i + ": Scan succeeded");
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