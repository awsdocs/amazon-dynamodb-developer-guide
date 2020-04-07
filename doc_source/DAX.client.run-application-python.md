# Python and DAX<a name="DAX.client.run-application-python"></a>

Follow this procedure to run the Python sample application on your Amazon EC2 instance\.

**To run the Python sample for DAX**

1. Install the DAX Python client using the `pip` utility\.

   ```
   pip install amazon-dax-client
   ```

1. Download the sample program source code \(`.zip` file\)\.

   ```
   wget http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/samples/TryDax.zip
   ```

   When the download is complete, extract the source files\.

   ```
   unzip TryDax.zip
   ```

1. Run the following Python programs\. The first program creates an Amazon DynamoDB table named `TryDaxTable`\. The second program writes data to the table\.

   ```
   python 01-create-table.py
   python 02-write-data.py
   ```

1. Run the following Python programs\.

   ```
   python 03-getitem-test.py
   python 04-query-test.py
   python 05-scan-test.py
   ```

    Take note of the timing information—the number of milliseconds required for the `GetItem`, `Query`, and `Scan` tests\.

1. In the previous step, you ran the programs against the DynamoDB endpoint\. Now run the programs again, but this time, the `GetItem`, `Query`, and `Scan` operations are processed by your DAX cluster\.

   To determine the endpoint for your DAX cluster, choose one of the following:
   + **Using the DynamoDB console** — Choose your DAX cluster\. The cluster endpoint is shown on the console, as in the following example\.

     ```
     mycluster.frfx8h.clustercfg.dax.usw2.amazonaws.com:8111
     ```
   + **Using the AWS CLI** — Enter the following command\.

     ```
     aws dax describe-clusters --query "Clusters[*].ClusterDiscoveryEndpoint"
     ```

     The cluster endpoint port and address are shown in the output, as in this example\.

     ```
     {
         "Port": 8111,
         "Address":"mycluster.frfx8h.clustercfg.dax.usw2.amazonaws.com"
     }
     ```

   Run the programs again, but this time, specify the cluster endpoint as a command line parameter\.

   ```
   python 03-getitem-test.py mycluster.frfx8h.clustercfg.dax.usw2.amazonaws.com:8111
   python 04-query-test.py mycluster.frfx8h.clustercfg.dax.usw2.amazonaws.com:8111
   python 05-scan-test.py mycluster.frfx8h.clustercfg.dax.usw2.amazonaws.com:8111
   ```

   Look at the rest of the output, and take note of the timing information\. The elapsed times for `GetItem`, `Query`, and `Scan` should be significantly lower with DAX than with DynamoDB\.

1. Run the following Python program to delete `TryDaxTable`\.

   ```
   python 06-delete-table.py
   ```

For more information about these programs, see the following sections:
+ [01\-create\-table\.py](DAX.client.run-application-python.01-create-table.md)
+ [02\-write\-data\.py](DAX.client.run-application-python.02-write-data.md)
+ [03\-getitem\-test\.py](DAX.client.run-application-python.03-getitem-test.md)
+ [04\-query\-test\.py](DAX.client.run-application-python.04-query-test.md)
+ [05\-scan\-test\.py](DAX.client.run-application-python.05-scan-test.md)
+ [06\-delete\-table\.py](DAX.client.run-application-python.06-delete-table.md)