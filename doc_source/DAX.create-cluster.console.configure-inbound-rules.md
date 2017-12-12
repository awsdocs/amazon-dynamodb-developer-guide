# Step 3: Configure Security Group Inbound Rules<a name="DAX.create-cluster.console.configure-inbound-rules"></a>

Your DAX cluster uses TCP port 8111 for communication, so you will need to authorize inbound traffic on that port\. This will allow Amazon EC2 instances in your Amazon VPC to access your DAX cluster\.

**Note**  
If you launched your DAX cluster with a different security group \(other than `default`\), you will need to perform this procedure for that group instead\.

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Choose the **default** security group\. In the **Actions** menu, choose **Edit inbound rules**\.

1. Choose **Add Rule**, and enter the following information:

   + **Port Range**—type **8111**

     **Source**—type **default**, and then choose the identifier for your default security group\.

   When the settings are as you want them, choose **Save**\.