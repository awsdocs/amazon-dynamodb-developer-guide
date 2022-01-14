# \.NET and DAX<a name="DAX.client.run-application-dotnet"></a>

Follow these steps to run the \.NET sample on your Amazon EC2 instance\.

**Note**  
This tutorial uses the \.NET 6 SDK\, but will work with the .NET Core SDK also. It shows how you can run a program in your default Amazon VPC to access your Amazon DynamoDB Accelerator \(DAX\) cluster\. If you prefer, you can use the AWS Toolkit for Visual Studio to write a \.NET application and deploy it into your VPC\.  
For more information, see [Creating and Deploying Elastic Beanstalk Applications in \.NET Using AWS Toolkit for Visual Studio](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/create_deploy_NET.html) in the *AWS Elastic Beanstalk Developer Guide*\.

**To run the \.NET sample for DAX**

1. Go to the [Microsoft Downloads page](hhttps://dotnet.microsoft.com/download?initial-os=linux) and download the latest \.NET 6 (or .NET Core) SDK for Linux\. The downloaded file is `dotnet-sdk-N.N.N-linux-x64.tar.gz`\.

2. Extract the SDK files.

   ```
   mkdir dotnet
   tar zxvf dotnet-sdk-N.N.N-linux-x64.tar.gz -C dotnet
   ```

   Replace `N.N.N` with the actual version number of the \.NET SDK \(for example: `6.0.100`\)\.

3. Verify the installation\.

   ```
   alias dotnet=$HOME/dotnet/dotnet
   dotnet --version
   ```

   This should print the version number of the \.NET SDK\.
**Note**  
Instead of the version number, you might receive the following error:  
error: libunwind\.so\.8: cannot open shared object file: No such file or directory  
To resolve the error, install the `libunwind` package\.  

   ```
   sudo yum install -y libunwind
   ```
After you do this, you should be able to run the `dotnet --version` command without any errors\.

1. Create a new \.NET project\.

   ```
   dotnet new console -o myApp 
   ```

   This requires a few minutes to perform a one\-time\-only setup\. When it is complete, run the sample project\.

   ```
   dotnet run --project myApp
   ```

   You should receive the following message: `Hello World!`

2. The `myApp/myApp.csproj` file contains metadata about your project\. To use the DAX client in your application, modify the file so that it looks like the following\.

   ```
   <Project Sdk="Microsoft.NET.Sdk">
       <PropertyGroup>
           <OutputType>Exe</OutputType>
           <TargetFramework>net6.0</TargetFramework>
       </PropertyGroup>
       <ItemGroup>
           <PackageReference Include="AWSSDK.DAX.Client" Version="*" />
       </ItemGroup>
   </Project>
   ```

3. Download the sample program source code \(`.zip` file\)\.

   ```
   wget http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/samples/TryDax.zip
   ```

   When the download is complete, extract the source files\.

   ```
   unzip TryDax.zip
   ```

4. Now run the sample programs, one at a time\. For each program, copy its contents into the `myApp/Program.cs`, and then run the `myApp` project\.

   Run the following \.NET programs\. The first program creates a DynamoDB table named `TryDaxTable`\. The second program writes data to the table\.

   ```
   cp TryDax/dotNetTryDax/dotNet/01-CreateTable.cs myApp/Program.cs
   dotnet run --project myApp
   
   cp TryDax/dotNetTryDax/dotNet/02-Write-Data.cs myApp/Program.cs
   dotnet run --project myApp
   ```

5. Next, run some programs to perform `GetItem`, `Query`, and `Scan` operations on your DAX cluster\. To determine the endpoint for your DAX cluster, choose one of the following:
   +  **Using the DynamoDB console** — Choose your DAX cluster\. The cluster endpoint is shown on the console, as in the following example\.

     ```
     dax://my-cluster.l6fzcv.dax-clusters.us-east-1.amazonaws.com
     ```
   + **Using the AWS CLI** — Enter the following command\.

     ```
     aws dax describe-clusters --query "Clusters[*].ClusterDiscoveryEndpoint"
     ```

     The cluster endpoint is shown in the output, as in the following example\.

     ```
     {
         "Address": "my-cluster.l6fzcv.dax-clusters.us-east-1.amazonaws.com",
         "Port": 8111,
         "URL": "dax://my-cluster.l6fzcv.dax-clusters.us-east-1.amazonaws.com"
     }
     ```

   Now run the following programs, specifying your cluster endpoint as a command line parameter\. \(Replace the sample endpoint with your actual DAX cluster endpoint\.\)

   ```
   cp TryDax/dotNet/03-GetItem-Test.cs myApp/Program.cs
   dotnet run --project myApp dax://my-cluster.l6fzcv.dax-clusters.us-east-1.amazonaws.com
   
   cp TryDax/dotNet/04-Query-Test.cs myApp/Program.cs
   dotnet run --project myApp dax://my-cluster.l6fzcv.dax-clusters.us-east-1.amazonaws.com
   
   cp TryDax/dotNet/05-Scan-Test.cs myApp/Program.cs
   dotnet run --project myApp dax://my-cluster.l6fzcv.dax-clusters.us-east-1.amazonaws.com
   ```

   Take note of the timing information—the number of milliseconds required for the `GetItem`, `Query`, and `Scan` tests\.

6. Run the following \.NET program to delete `TryDaxTable`\.

   ```
   cp TryDax/dotNet/06-DeleteTable.cs myApp/Program.cs
   dotnet run --project myApp
   ```

For more information about these programs, see the following sections:
+ [01\-CreateTable\.cs](DAX.client.run-application-dotnet.01-CreateTable.md)
+ [02\-Write\-Data\.cs](DAX.client.run-application-dotnet.02-Write-Data.md)
+ [03\-GetItem\-Test\.cs](DAX.client.run-application-dotnet.03-GetItem-Test.md)
+ [04\-Query\-Test\.cs](DAX.client.run-application-dotnet.04-Query-Test.md)
+ [05\-Scan\-Test\.cs](DAX.client.run-application-dotnet.05-Scan-Test.md)
+ [06\-DeleteTable\.cs](DAX.client.run-application-dotnet.06-DeleteTable.md)