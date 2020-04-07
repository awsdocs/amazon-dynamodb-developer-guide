# DAX SDK for Go<a name="DAX.client.run-application-go"></a>

Follow this procedure to run the Amazon DynamoDB Accelerator \(DAX\) SDK for Go sample application on your Amazon EC2 instance\.

**To run the SDK for Go sample for DAX**

1. Set up the SDK for Go on your Amazon EC2 instance:

   1. Install the Go programming language \(`Golang`\)\.

      ```
      sudo yum install -y golang
      ```

   1. Set the `GOPATH` environment variable\.

      ```
      # GOROOT is the location where Go package is installed on your system
      export GOROOT=/usr/lib/golang
      
      # GOPATH is the location of your work directory
      export GOPATH=$HOME/projects
      
      # PATH in order to access go binary system wide
      export PATH=$PATH:$GOROOT/bin
      ```
**Note**  
The preceding commands set the environment variables for your current session only\. To make these settings permanent, add the commands in the `~/.bash_profile` file\.

   1. Test that Golang is installed and running correctly\.

      ```
      go version
      ```

      The following message should appear\.

      `go version go1.9.6 linux/amd64/`

1. Install the DAX Golang client\.

   ```
   go get github.com/aws/aws-dax-go
   ```

1. Install the sample Golang application\.

   ```
   go get github.com/aws-samples/aws-dax-go-sample
   ```

1. Run the following Golang programs\. The first program creates a DynamoDB table named `TryDaxGoTable`\. The second program writes data to the table\.

   ```
   go run $GOPATH/src/github.com/aws-samples/aws-dax-go-sample/try_dax.go -service dynamodb -command create-table
   ```

   ```
   go run $GOPATH/src/github.com/aws-samples/aws-dax-go-sample/try_dax.go -service dynamodb -command put-item
   ```

1. Run the following Golang programs\.

   ```
   go run $GOPATH/src/github.com/aws-samples/aws-dax-go-sample/try_dax.go -service dynamodb -command get-item
   ```

   ```
   go run $GOPATH/src/github.com/aws-samples/aws-dax-go-sample/try_dax.go -service dynamodb -command query
   ```

   ```
   go run $GOPATH/src/github.com/aws-samples/aws-dax-go-sample/try_dax.go -service dynamodb -command scan
   ```

   Take note of the timing information—the number of milliseconds required for the `GetItem`, `Query`, and `Scan` tests\.

1. In the previous step, you ran the programs against the DynamoDB endpoint\. Now, run the programs again, but this time, the `GetItem`, `Query`, and `Scan` operations are processed by your DAX cluster\.

   To determine the endpoint for your DAX cluster, choose one of the following:
   + **Using the DynamoDB console** — Choose your DAX cluster\. The cluster endpoint is shown on the console, as in the following example\.

     ```
     mycluster.frfx8h.clustercfg.dax.usw2.amazonaws.com:8111
     ```
   + **Using the AWS CLI** — Enter the following command\.

     ```
     aws dax describe-clusters --query "Clusters[*].ClusterDiscoveryEndpoint"
     ```

     The cluster endpoint port and address are shown in the output, as in the following example\.

     ```
     {
         "Port": 8111,
         "Address":"mycluster.frfx8h.clustercfg.dax.usw2.amazonaws.com"
     }
     ```

   Now run the programs again, but this time, specify the cluster endpoint as a command line parameter\.

   ```
   go run $GOPATH/src/github.com/aws-samples/aws-dax-go-sample/try_dax.go -service dax -command get-item -endpoint mycluster.frfx8h.clustercfg.dax.usw2.amazonaws.com:8111
   ```

   ```
   go run $GOPATH/src/github.com/aws-samples/aws-dax-go-sample/try_dax.go -service dax -command query -endpoint mycluster.frfx8h.clustercfg.dax.usw2.amazonaws.com:8111
   ```

   ```
   go run $GOPATH/src/github.com/aws-samples/aws-dax-go-sample/try_dax.go -service dax -command scan -endpoint mycluster.frfx8h.clustercfg.dax.usw2.amazonaws.com:8111
   ```

   Look at the rest of the output, and take note of the timing information\. The elapsed times for `GetItem`, `Query`, and `Scan` should be significantly lower with DAX than with DynamoDB\.

1. Run the following Golang program to delete `TryDaxGoTable`\.

   ```
   go run $GOPATH/src/github.com/aws-samples/aws-dax-go-sample/try_dax.go -service dynamodb -command delete-table
   ```