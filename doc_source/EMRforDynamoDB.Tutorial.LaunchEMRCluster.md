# Step 2: Launch an Amazon EMR cluster<a name="EMRforDynamoDB.Tutorial.LaunchEMRCluster"></a>

In this step, you will configure and launch an Amazon EMR cluster\. Hive and a storage handler for DynamoDB will already be installed on the cluster\.

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/emr](https://console.aws.amazon.com/emr/)\.

1. Choose **Create Cluster**\.

1. On the **Create Cluster \- Quick Options** page, do the following:

   1. In **Cluster name**, type a name for your cluster \(for example: `My EMR cluster`\)\.

   1. In **EC2 key pair**, choose the key pair you created earlier\.

   Leave the other settings at their defaults\.

1. Choose **Create cluster**\.

It will take several minutes to launch your cluster\. You can use the **Cluster Details** page in the Amazon EMR console to monitor its progress\.

When the status changes to `Waiting`, the cluster is ready for use\.

## Cluster log files and Amazon S3<a name="EMRforDynamoDB.Tutorial.LaunchEMRCluster.LogFilesAndS3"></a>

An Amazon EMR cluster generates log files that contain information about the cluster status and debugging information\. The default settings for **Create Cluster \- Quick Options** include setting up Amazon EMR logging\.

If one does not already exist, the AWS Management Console creates an Amazon S3 bucket\. The bucket name is `aws-logs-account-id-region`, where ` account-id` is your AWS account number and `region` is the region in which you launched the cluster \(for example, `aws-logs-123456789012-us-west-2`\)\.

**Note**  
You can use the Amazon S3 console to view the log files\. For more information, see [View Log Files](https://docs.aws.amazon.com/ElasticMapReduce/latest/ManagementGuide/emr-manage-view-web-log-files.html) in the *Amazon EMR Management Guide*\.

You can use this bucket for purposes in addition to logging\. For example, you can use the bucket as a location for storing a Hive script or as a destination when exporting data from Amazon DynamoDB to Amazon S3\.

**Next step**  
[Step 3: Connect to the Leader node](EMRforDynamoDB.Tutorial.ConnectToLeaderNode.md)