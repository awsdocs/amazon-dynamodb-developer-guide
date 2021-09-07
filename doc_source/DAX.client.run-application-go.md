# DAX SDK for Go<a name="DAX.client.run-application-go"></a>

Follow this procedure to run the Amazon DynamoDB Accelerator \(DAX\) SDK for Go sample application on your Amazon EC2 instance\.

**To run the SDK for Go sample for DAX**

1. Set up the SDK for Go on your Amazon EC2 instance:

   1. Install the Go programming language \(`Golang`\)\.

      ```
      sudo yum install -y golang
      ```

   1. Test that Golang is installed and running correctly\.

      ```
      go version
      ```

      A message like this should appear\.

      ```
      go version go1.15.5 linux/amd64
      ```

      The remaining instructions rely on module support, which became the default with Go version 1\.13\.

1. Install the sample Golang application\.

   ```
   go get github.com/aws-samples/aws-dax-go-sample
   ```

1. Run the following Golang programs\. The first program creates a DynamoDB table named `TryDaxGoTable`\. The second program writes data to the table\.

   ```
   go run ~/go/src/github.com/aws-samples/aws-dax-go-sample/try_dax.go -service dynamodb -command create-table
   ```

   ```
   go run ~/go/src/github.com/aws-samples/aws-dax-go-sample/try_dax.go -service dynamodb -command put-item
   ```

1. Run the following Golang programs\.

   ```
   go run ~/go/src/github.com/aws-samples/aws-dax-go-sample/try_dax.go -service dynamodb -command get-item
   ```

   ```
   go run ~/go/src/github.com/aws-samples/aws-dax-go-sample/try_dax.go -service dynamodb -command query
   ```

   ```
   go run ~/go/src/github.com/aws-samples/aws-dax-go-sample/try_dax.go -service dynamodb -command scan
   ```

   Take note of the timing information—the number of milliseconds required for the `GetItem`, `Query`, and `Scan` tests\.

1. In the previous step, you ran the programs against the DynamoDB endpoint\. Now, run the programs again, but this time, the `GetItem`, `Query`, and `Scan` operations are processed by your DAX cluster\.

   To determine the endpoint for your DAX cluster, choose one of the following:
   + **Using the DynamoDB console** — Choose your DAX cluster\. The cluster endpoint is shown on the console, as in the following example\.

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

   Now run the programs again, but this time, specify the cluster endpoint as a command line parameter\.

   ```
   go run ~/go/src/github.com/aws-samples/aws-dax-go-sample/try_dax.go -service dax -command get-item -endpoint dax://my-cluster.l6fzcv.dax-clusters.us-east-1.amazonaws.com
   ```

   ```
   go run ~/go/src/github.com/aws-samples/aws-dax-go-sample/try_dax.go -service dax -command query -endpoint dax://my-cluster.l6fzcv.dax-clusters.us-east-1.amazonaws.com
   ```

   ```
   go run ~/go/src/github.com/aws-samples/aws-dax-go-sample/try_dax.go -service dax -command scan -endpoint dax://my-cluster.l6fzcv.dax-clusters.us-east-1.amazonaws.com
   ```

   Look at the rest of the output, and take note of the timing information\. The elapsed times for `GetItem`, `Query`, and `Scan` should be significantly lower with DAX than with DynamoDB\.

1. Run the following Golang program to delete `TryDaxGoTable`\.

   ```
   go run ~/go/src/github.com/aws-samples/aws-dax-go-sample/try_dax.go -service dynamodb -command delete-table
   ```