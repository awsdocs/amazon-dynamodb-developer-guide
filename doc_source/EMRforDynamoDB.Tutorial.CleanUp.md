# Step 7: \(Optional\) clean up<a name="EMRforDynamoDB.Tutorial.CleanUp"></a>

Now that you have completed the tutorial, you can continue reading this section to learn more about working with DynamoDB data in Amazon EMR\. You might decide to keep your Amazon EMR cluster up and running while you do this\.

If you don't need the cluster anymore, you should terminate it and remove any associated resources\. This will help you avoid being charged for resources you don't need\.

1. Terminate the Amazon EMR cluster:

   1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/emr](https://console.aws.amazon.com/emr/)\.

   1. Choose the Amazon EMR cluster, choose **Terminate**, and then confirm\.

1. Delete the Features table in DynamoDB:

   1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

   1. In the navigation pane, choose **Tables**\.

   1. Choose the Features table\. From the **Actions** menu, choose **Delete Table**\.

1. Delete the Amazon S3 bucket containing the Amazon EMR log files:

   1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

   1. From the list of buckets, choose `aws-logs- accountID-region`, where *accountID* is your AWS account number and *region* is the region in which you launched the cluster\.

   1. From the **Action** menu, choose **Delete**\.