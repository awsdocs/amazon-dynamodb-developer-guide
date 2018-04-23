# Amazon VPC Endpoints for DynamoDB<a name="vpc-endpoints-dynamodb"></a>

For security reasons, many AWS customers run their applications within an Amazon Virtual Private Cloud environment \(Amazon VPC\)\. With Amazon VPC, you can launch Amazon EC2 instances into a virtual private cloud, which is logically isolated from other networks—including the public Internet\. With an Amazon VPC, you have control over its IP address range, subnets, routing tables, network gateways, and security settings\.

**Note**  
If you created your AWS account after 2013\-12\-04, then you already have a default VPC in each AWS region\. A default VPC is ready for you to use—you can immediately start using your default VPC without having to perform any additional configuration steps\.  
For more information, see [Your Default VPC and Subnets](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/default-vpc.html) in the Amazon VPC User Guide\.

In order to access the public Internet, your VPC must have an Internet gateway—a virtual router that connects your VPC to the Internet\. This allows applications running on Amazon EC2 in your VPC to access Internet resources, such as Amazon DynamoDB\.

By default, communications to and from DynamoDB use the HTTPS protocol, which protects network traffic by using SSL/TLS encryption\. The following diagram shows how an EC2 instance in a VPC accesses DynamoDB:

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/ddb-no-vpc-endpoint.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

Many customers have legitimate privacy and security concerns about sending and receiving data across the public Internet\. Customers can address these concerns by using a virtual private network \(VPN\) to route all DynamoDB network traffic through the customer's own corporate network infrastructure\. However, this approach can introduce bandwidth and availability challenges\.

VPC endpoints for DynamoDB can alleviate these challenges\. A *VPC endpoint* for DynamoDB enables Amazon EC2 instances in your VPC to use their private IP addresses to access DynamoDB with no exposure to the public Internet\. Your EC2 instances do not require public IP addresses, and you do not need an Internet gateway, a NAT device, or a virtual private gateway in your VPC\. You use endpoint policies to control access to DynamoDB\. Traffic between your VPC and the AWS service does not leave the Amazon network\.

When you create a VPC endpoint for DynamoDB, any requests to a DynamoDB endpoint within the region \(for example, *dynamodb\.us\-west\-2\.amazonaws\.com*\) are routed to a private DynamoDB endpoint within the Amazon network\. You do not need to modify your applications running on EC2 instances in your VPC—the endpoint name remains the same, but the route to DynamoDB stays entirely within the Amazon network, and does not access the public Internet\.

The following diagram shows how an EC2 instance in a VPC can use a VPC endpoint to access DynamoDB\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/ddb-yes-vpc-endpoint.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

## Tutorial: Using a VPC Endpoint for DynamoDB<a name="vpc-endpoints-dynamodb-tutorial"></a>

**Topics**
+ [Step 1: Launch an Amazon EC2 Instance](#vpc-endpoints-dynamodb-tutorial.launch-ec2-instance)
+ [Step 2: Configure Your Amazon EC2 Instance](#vpc-endpoints-dynamodb-tutorial.configure-ec2-instance)
+ [Step 3: Create a VPC Endpoint for DynamoDB](#vpc-endpoints-dynamodb-tutorial.create-endpoint)
+ [Step 4: \(Optional\) Clean Up](#vpc-endpoints-dynamodb-tutorial.clean-up)

This section walks you through setting and using a VPC endpoint for DynamoDB\.

### Step 1: Launch an Amazon EC2 Instance<a name="vpc-endpoints-dynamodb-tutorial.launch-ec2-instance"></a>

In this step, you launch an Amazon EC2 instance in your default Amazon VPC\. You will then be able to create and use a VPC endpoint for DynamoDB\.

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch Instance** and do the following:

   Step 1: Choose an Amazon Machine Image \(AMI\)
   + At the top of the list of AMIs, go to **Amazon Linux AMI** and choose **Select**\.

   Step 2: Choose an Instance Type
   + At the top of the list of instance types, choose **t2\.micro**\.
   + Choose **Next: Configure Instance Details**\.

   Step 3: Configure Instance Details
   + Go to **Network** and choose your default VPC\.

     Choose **Next: Add Storage**\.

   Step 4: Add Storage
   + Skip this step by choosing **Next: Tag Instance**\.

   Step 5: Tag Instance
   + Skip this step by choosing **Next: Configure Security Group**\.

   Step 6: Configure Security Group
   + Choose **Select an existing security group**\.
   + In the list of security groups, choose **default**\. This is the default security group for your VPC\.
   + Choose **Next: Review and Launch**\.

   Step 7: Review Instance Launch
   + Choose **Launch**\.

1. In the **Select an existing key pair or create a new key pair** window, do one of the following:
   + If you do not have an Amazon EC2 key pair, choose **Create a new key pair** and follow the instructions\. You will be asked to download a private key file \(*\.pem* file\); you will need this file later when you log in to your Amazon EC2 instance\.
   + If you already have an existing Amazon EC2 key pair, go to **Select a key pair** and choose your key pair from the list\. Note that you must already have the private key file \( *\.pem* file\) available in order to log in to your Amazon EC2 instance\.

1. When you have configured your key pair, choose **Launch Instances\.**

1. Return to the Amazon EC2 console home page and choose the instance that you launched\. In the lower pane, on the **Description** tab, find the **Public DNS** for your instance\. For example: `ec2-00-00-00-00.us-east-1.compute.amazonaws.com`\.

   Make a note of this public DNS name, because you will need it in the next step in this tutorial \([Step 2: Configure Your Amazon EC2 Instance](#vpc-endpoints-dynamodb-tutorial.configure-ec2-instance)\)\.

**Note**  
It will take a few minutes for your Amazon EC2 instance to become available\. Before you go on to the next step, ensure that the **Instance State** is `running` and that all of its **Status Checks** have passed\.

### Step 2: Configure Your Amazon EC2 Instance<a name="vpc-endpoints-dynamodb-tutorial.configure-ec2-instance"></a>

When your Amazon EC2 instance is available, you will be able to log into it and prepare it for first use\.

**Note**  
The following steps assume that you are connecting to your Amazon EC2 instance from a computer running Linux\. For other ways to connect, see [Connect to Your Linux Instance](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstances.html) in the Amazon EC2 User Guide for Linux Instances\.

1. You will need to authorize inbound SSH traffic to your Amazon EC2 instance\. To do this, you will create a new EC2 security group, and then assign the security group to your EC2 instance\.

   1. In the navigation pane, choose **Security Groups**\.

   1. Choose **Create Security Group**\. In the **Create Security Group** window, do the following:
      + **Security group name**—type a name for your security group\. For example: `my-ssh-access`
      + **Description**—type a short description for the security group\.
      + **VPC**—choose your default VPC\.
      + In the **Security group rules** section, choose **Add Rule** and do the following:
        + **Type**—choose SSH\.
        + **Source**—choose My IP\.

      When the settings are as you want them, choose **Create**\.

   1. In the navigation pane, choose **Instances**\.

   1. Choose the Amazon EC2 instance that you launched in [Step 1: Launch an Amazon EC2 Instance](#vpc-endpoints-dynamodb-tutorial.launch-ec2-instance)\.

   1. Choose **Actions** \-\-> **Networking** \-\-> **Change Security Groups**\.

   1. In the **Change Security Groups**, select the security group that you created earlier in this procedure \(for example: `my-ssh-access`\)\. The existing `default` security group should also be selected\. When the settings are as you want them, choose **Assign Security Groups**\.

1. Use the `ssh` command to log in to your Amazon EC2 instance\. For example:

   ```
   ssh -i my-keypair.pem ec2-user@public-dns-name
   ```

   You will need to specify your private key file \(*\.pem* file\) and the public DNS name of your instance\. \(See [Step 1: Launch an Amazon EC2 Instance](#vpc-endpoints-dynamodb-tutorial.launch-ec2-instance)\)\. 

   The login ID is `ec2-user`\. No password is required\.

1. Configure your AWS credentials, as shown below\. Enter your AWS access key ID, secret key, and default region name when prompted:

   ```
   aws configure
    
   AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE
   AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
   Default region name [None]: us-east-1
   Default output format [None]:
   ```

You are now ready to create a VPC endpoint for DynamoDB\.

### Step 3: Create a VPC Endpoint for DynamoDB<a name="vpc-endpoints-dynamodb-tutorial.create-endpoint"></a>

In this step, you will create a VPC endpoint for DynamoDB and test it to make sure that it works\.

1. Before you begin, verify that you can communicate with DynamoDB using its public endpoint:

   ```
   aws dynamodb list-tables
   ```

   The output will show a list of DynamoDB tables that you currently own\. \(If you don't have any tables, the list will be empty\.\)\.

1. Verify that DynamoDB is an available service for creating VPC endpoints in the current AWS region\. \(The command is shown in bold text, followed by example output\.\)

   ```
   aws ec2 describe-vpc-endpoint-services
    
   {
       "ServiceNames": [
           "com.amazonaws.us-east-1.s3",
           "com.amazonaws.us-east-1.dynamodb"
       ]
   }
   ```

   In the example output, DynamoDB is one of the services available, so you can proceed with creating a VPC endpoint for it\.

1. Determine your VPC identifier\.

   ```
   aws ec2 describe-vpcs
    
   {
       "Vpcs": [
           {
               "VpcId": "vpc-0bbc736e", 
               "InstanceTenancy": "default", 
               "State": "available", 
               "DhcpOptionsId": "dopt-8454b7e1", 
               "CidrBlock": "172.31.0.0/16", 
               "IsDefault": true
           }
       ]
   }
   ```

   In the example output, the VPC ID is `vpc-0bbc736e`\.

1. Create the VPC endpoint\. For the `--vpc-id` parameter, specify the VPC ID from the previous step\.

   ```
   aws ec2 create-vpc-endpoint --vpc-id vpc-0bbc736e --service-name com.amazonaws.us-east-1.dynamodb
    
   {
       "VpcEndpoint": {
           "PolicyDocument": "{\"Version\":\"2008-10-17\",\"Statement\":[{\"Effect\":\"Allow\",\"Principal\":\"*\",\"Action\":\"*\",\"Resource\":\"*\"}]}", 
           "VpcId": "vpc-0bbc736e", 
           "State": "available", 
           "ServiceName": "com.amazonaws.us-east-1.dynamodb", 
           "RouteTableIds": [], 
           "VpcEndpointId": "vpce-9b15e2f2", 
           "CreationTimestamp": "2017-07-26T22:00:14Z"
       }
   }
   ```

1. Verify that you can access DynamoDB through the VPC endpoint:

   ```
   aws dynamodb list-tables
   ```

   If you want, you can try some other AWS CLI commands for DynamoDB\. For more information, see the [AWS CLI Command Reference](http://docs.aws.amazon.com/cli/latest/reference/)\.

### Step 4: \(Optional\) Clean Up<a name="vpc-endpoints-dynamodb-tutorial.clean-up"></a>

If you want to delete the resources you have created in this tutorial, follow these procedures:

**To remove your VPC endpoint for DynamoDB**

1. Log in to your Amazon EC2 instance\.

1. Determine the VPC endpoint ID:

   ```
   aws ec2 describe-vpc-endpoints
    
   {
       "VpcEndpoint": {
           "PolicyDocument": "{\"Version\":\"2008-10-17\",\"Statement\":[{\"Effect\":\"Allow\",\"Principal\":\"*\",\"Action\":\"*\",\"Resource\":\"*\"}]}", 
           "VpcId": "vpc-0bbc736e", 
           "State": "available", 
           "ServiceName": "com.amazonaws.us-east-1.dynamodb", 
           "RouteTableIds": [], 
           "VpcEndpointId": "vpce-9b15e2f2", 
           "CreationTimestamp": "2017-07-26T22:00:14Z"
       }
   }
   ```

   In the example output, the VPC endpoint ID is `vpce-9b15e2f2`\.

1. Delete the VPC endpoint:

   ```
   aws ec2 delete-vpc-endpoints --vpc-endpoint-ids vpce-9b15e2f2
    
   {
       "Unsuccessful": []
   }
   ```

   The empty array `[]` indicates success \(there were no unsuccessful requests\)\.

**To terminate your Amazon EC2 instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Choose your Amazon EC2 instance\.

1. Choose **Actions**, **Instance State**, **Terminate**\.

1. In the confirmation window, choose **Yes, Terminate**\.