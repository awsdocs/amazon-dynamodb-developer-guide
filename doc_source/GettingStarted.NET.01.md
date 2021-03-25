# Step 1: Create a DynamoDB Client<a name="GettingStarted.NET.01"></a>

The first step in the [Microsoft \.NET and DynamoDB Tutorial](GettingStarted.NET.md) is to create a client that gives you access to the Amazon DynamoDB API\. The `Main` function in `DynamoDB_intro` does this by calling a `createClient` function implemented in the `01_CreateClient.cs` file\.

```
using System;
using System.Net;
using System.Net.NetworkInformation;
using Amazon.DynamoDBv2;

namespace DynamoDB_intro
{
  public static partial class DdbIntro
  {
        /*-----------------------------------------------------------------------------------
          *  If you are creating a client for the Amazon DynamoDB service, make sure your credentials
          *  are set up first, as explained in:
          *  https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/SettingUp.DynamoWebService.html,
          *
          *  If you are creating a client for DynamoDBLocal (for testing purposes),
          *  DynamoDB-Local should be started first. For most simple testing, you can keep
          *  data in memory only, without writing anything to disk.  To do this, use the
          *  following command line:
          *
          *    java -Djava.library.path=./DynamoDBLocal_lib -jar DynamoDBLocal.jar -inMemory
          *
          *  For information about DynamoDBLocal, see:
          *  https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DynamoDBLocal.html.
          *-----------------------------------------------------------------------------------*/

        // So we know whether local DynamoDB is running
        private static readonly string Ip = "localhost";
        private static readonly int Port = 8000;
        private static readonly string EndpointUrl = "http://" + Ip + ":" + Port;

        private static bool IsPortInUse()
        {
            bool isAvailable = true;

            // Evaluate current system TCP connections. This is the same information provided
            // by the netstat command line application, just in .Net strongly-typed object
            // form.  We will look through the list, and if our port we would like to use
            // in our TcpClient is occupied, we will set isAvailable to false.
            IPGlobalProperties ipGlobalProperties = IPGlobalProperties.GetIPGlobalProperties();
            IPEndPoint[] tcpConnInfoArray = ipGlobalProperties.GetActiveTcpListeners();

            foreach (IPEndPoint endpoint in tcpConnInfoArray)
            {
                if (endpoint.Port == Port)
                {
                    isAvailable = false;
                    break;
                }
            }

            return isAvailable;
        }
       
    public static bool createClient(bool useDynamoDbLocal)
    {
      if (useDynamoDbLocal)
      {
        // First, check to see whether anyone is listening on the DynamoDB local port
        // (by default, this is port 8000, so if you are using a different port, modify this accordingly)
        var portUsed = IsPortInUse();

        if (portUsed)
        {
            Console.WriteLine("The local version of DynamoDB is NOT running.");
            return (false);
        }

        // DynamoDB-Local is running, so create a client
        Console.WriteLine( "  -- Setting up a DynamoDB-Local client (DynamoDB Local seems to be running)" );
        AmazonDynamoDBConfig ddbConfig = new AmazonDynamoDBConfig();
        ddbConfig.ServiceURL = EndpointUrl;
        try
        {
            Client = new AmazonDynamoDBClient(ddbConfig);
        }
        catch( Exception ex )
        {
          Console.WriteLine( "     FAILED to create a DynamoDBLocal client; " + ex.Message );
          return false;
        }
      }
      else
      {
          Client = new AmazonDynamoDBClient();
      }
     
      return true;
    }
  }
}
```

`Main` calls this function with the *useDynamoDBLocal* parameter set to `true`\. Therefore the local test version of DynamoDB must already be running on your computer using the default port \(8000\), or the call fails\. If you do not have it installed yet, see [Running DynamoDB on Your Computer](DynamoDBLocal.md)\.

Setting the *useDynamoDBLocal* parameter to `false` creates a client for the DynamoDB service itself rather than the local test program\.

## Next Step<a name="GettingStarted.NET.01.NextStep"></a>

[Step 2: Create a DynamoDB Table Using the Low\-Level API](GettingStarted.NET.02.md)