# Using Amazon VPC endpoints to access DynamoDB<a name="vpc-endpoints-dynamodb"></a>

For security reasons, many AWS customers run their applications within an Amazon Virtual Private Cloud environment \(Amazon VPC\)\. With Amazon VPC, you can launch Amazon EC2 instances into a virtual private cloud, which is logically isolated from other networks—including the public internet\. With an Amazon VPC, you have control over its IP address range, subnets, routing tables, network gateways, and security settings\.

**Note**  
If you created your AWS account after December 4, 2013, then you already have a default VPC in each AWS Region\. A default VPC is ready for you to use—you can immediately start using it without having to perform any additional configuration steps\.  
For more information, see [Default VPC and Default Subnets](https://docs.aws.amazon.com/vpc/latest/userguide/default-vpc.html) in the *Amazon VPC User Guide*\.

To access the public internet, your VPC must have an internet gateway—a virtual router that connects your VPC to the internet\. This allows applications running on Amazon EC2 in your VPC to access internet resources, such as Amazon DynamoDB\.

By default, communications to and from DynamoDB use the HTTPS protocol, which protects network traffic by using SSL/TLS encryption\. The following diagram shows how an EC2 instance in a VPC accesses DynamoDB:

![\[Workflow diagram showing an EC2 instance accessing DynamoDB through a router, internet gateway, and the internet.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/ddb-no-vpc-endpoint.png)![\[Workflow diagram showing an EC2 instance accessing DynamoDB through a router, internet gateway, and the internet.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Workflow diagram showing an EC2 instance accessing DynamoDB through a router, internet gateway, and the internet.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

Many customers have legitimate privacy and security concerns about sending and receiving data across the public internet\. You can address these concerns by using a virtual private network \(VPN\) to route all DynamoDB network traffic through your own corporate network infrastructure\. However, this approach can introduce bandwidth and availability challenges\.

VPC endpoints for DynamoDB can alleviate these challenges\. A *VPC endpoint* for DynamoDB enables Amazon EC2 instances in your VPC to use their private IP addresses to access DynamoDB with no exposure to the public internet\. Your EC2 instances do not require public IP addresses, and you don't need an internet gateway, a NAT device, or a virtual private gateway in your VPC\. You use endpoint policies to control access to DynamoDB\. Traffic between your VPC and the AWS service does not leave the Amazon network\.

When you create a VPC endpoint for DynamoDB, any requests to a DynamoDB endpoint within the Region \(for example, *dynamodb\.us\-west\-2\.amazonaws\.com*\) are routed to a private DynamoDB endpoint within the Amazon network\. You don't need to modify your applications running on EC2 instances in your VPC\. The endpoint name remains the same, but the route to DynamoDB stays entirely within the Amazon network, and does not access the public internet\.

The following diagram shows how an EC2 instance in a VPC can use a VPC endpoint to access DynamoDB\.

![\[Workflow diagram showing an EC2 instance accessing DynamoDB through a router and VPC endpoint only.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/ddb-yes-vpc-endpoint.png)![\[Workflow diagram showing an EC2 instance accessing DynamoDB through a router and VPC endpoint only.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Workflow diagram showing an EC2 instance accessing DynamoDB through a router and VPC endpoint only.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

For more information, see [Tutorial: Using a VPC endpoint for DynamoDB](vpc-endpoints-dynamodb-tutorial.md)\.