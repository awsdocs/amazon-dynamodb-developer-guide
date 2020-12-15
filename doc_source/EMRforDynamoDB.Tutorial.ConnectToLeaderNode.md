# Step 3: Connect to the Leader Node<a name="EMRforDynamoDB.Tutorial.ConnectToLeaderNode"></a>

When the status of your Amazon EMR cluster changes to `Waiting`, you will be able to connect to the leader node using SSH and perform command line operations\.

1. In the Amazon EMR console, choose your cluster's name to view its status\.

1. On the **Cluster Details** page, find the **Leader public DNS** field\. This is the public DNS name for the leader node of your Amazon EMR cluster\.

1. To the right of the DNS name, choose the **SSH** link\.

1. Follow the instructions in **Connect to the Leader Node Using SSH **\.

   Depending on your operating system, choose the **Windows** tab or the **Mac/Linux** tab, and follow the instructions for connecting to the leader node\.

After you connect to the leader node using either SSH or PuTTY, you should see a command prompt similar to the following:

```
[hadoop@ip-192-0-2-0 ~]$ 
```

**Next Step**  
[Step 4: Load Data into HDFS](EMRforDynamoDB.Tutorial.LoadDataIntoHDFS.md)