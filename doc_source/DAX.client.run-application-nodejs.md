# Node\.js and DAX<a name="DAX.client.run-application-nodejs"></a>

Follow these steps to run the Node\.js sample application on your Amazon EC2 instance\.

**To run the Node\.js sample for DAX**

1. Set up Node\.js on your Amazon EC2 instance, as follows:

   1. Install node version manager \(`nvm`\)\.

      ```
      curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash
      ```

   1. Use nvm to install Node\.js\.

      ```
      nvm install 12.16.3
      ```

   1. Test that Node\.js is installed and running correctly\.

      ```
      node -e "console.log('Running Node.js ' + process.version)"
      ```

      This should display the following message\.

      `Running Node.js v12.16.3`

1. Install the DAX Node\.js client using the node package manager \(`npm`\)\.

   ```
   npm install amazon-dax-client
   ```

1. Download the sample program source code \(`.zip` file\)\.

   ```
   wget http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/samples/TryDax.zip
   ```

   When the download is complete, extract the source files\.

   ```
   unzip TryDax.zip
   ```

1. Run the following Node\.js programs\. The first program creates an Amazon DynamoDB table named `TryDaxTable`\. The second program writes data to the table\.

   ```
   node 01-create-table.js
   node 02-write-data.js
   ```

1. Run the following Node\.js programs\.

   ```
   node 03-getitem-test.js
   node 04-query-test.js
   node 05-scan-test.js
   ```

    Take note of the timing information—the number of milliseconds required for the `GetItem`, `Query`, and `Scan` tests\.

1. In the previous step, you ran the programs against the DynamoDB endpoint\. Run the programs again, but this time, the `GetItem`, `Query` and `Scan` operations are processed by your DAX cluster\.

   To determine the endpoint for your DAX cluster, choose one of the following\.
   + **Using the DynamoDB console**—Choose your DAX cluster\. The cluster endpoint is shown on the console, as in the following example\.

     ```
     mycluster.frfx8h.clustercfg.dax.usw2.amazonaws.com:8111
     ```
   + **Using the AWS CLI**—Enter the following command\.

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
   node 03-getitem-test.js mycluster.frfx8h.clustercfg.dax.usw2.amazonaws.com:8111
   node 04-query-test.js mycluster.frfx8h.clustercfg.dax.usw2.amazonaws.com:8111
   node 05-scan-test.js mycluster.frfx8h.clustercfg.dax.usw2.amazonaws.com:8111
   ```

   Look at the rest of the output, and take note of the timing information\. The elapsed times for `GetItem`, `Query`, and `Scan` should be significantly lower with DAX than with DynamoDB\.

1. Run the following Node\.js program to delete `TryDaxTable`\.

   ```
   node 06-delete-table
   ```

For more information about these programs, see the following sections:
+ [01\-create\-table\.js](DAX.client.run-application-nodejs.01-create-table.md)
+ [02\-write\-data\.js](DAX.client.run-application-nodejs.02-write-data.md)
+ [03\-getitem\-test\.js](DAX.client.run-application-nodejs.03-getitem-test.md)
+ [04\-query\-test\.js](DAX.client.run-application-nodejs.04-query-test.md)
+ [05\-scan\-test\.js](DAX.client.run-application-nodejs.05-scan-test.md)
+ [06\-delete\-table\.js](DAX.client.run-application-nodejs.06-delete-table.md)