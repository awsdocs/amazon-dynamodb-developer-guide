# Internetwork traffic privacy<a name="inter-network-traffic-privacy"></a>

Connections are protected both between Amazon DynamoDB and on\-premises applications and between DynamoDB and other AWS resources within the same AWS Region\.

## Required policy for endpoints<a name="inter-network-traffic-DescribeEndpoints"></a>

Amazon DynamoDB provides a `DescribeEndpoints` API that enables you to enumerate regional endpoint information\. Any policies that refer to a Virtual Private Cloud \(VPC\) endpoint must authorize the `DescribeEndpoints` API call for the requesting Identity and Access Management \(IAM\) principal\(s\)\. This is done with the new IAM action \(dynamodb:DescribeEndpoints\)\. You must include the "dynamodb:DescribeEndpoints" action in your IAM and VPC endpoint policies, or access to the `DescribeEndpoints` API will be denied\. 

The following is an example of an endpoints policy\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": "(Include IAM Principals)",
            "Action": "dynamodb:DescribeEndpoints",
            "Resource": "*"
        }
    ]
}
```

## Traffic between service and on\-premises clients and applications<a name="inter-network-traffic-privacy-on-prem"></a>

You have two connectivity options between your private network and AWS: 
+ An AWS Site\-to\-Site VPN connection\. For more information, see [What is AWS Site\-to\-Site VPN?](https://docs.aws.amazon.com/vpn/latest/s2svpn/VPC_VPN.html) in the *AWS Site\-to\-Site VPN User Guide*\.
+ An AWS Direct Connect connection\. For more information, see [What is AWS Direct Connect?](https://docs.aws.amazon.com/directconnect/latest/UserGuide/Welcome.html) in the *AWS Direct Connect User Guide*\.

Access to DynamoDB via the network is through AWS published APIs\. Clients must support Transport Layer Security \(TLS\) 1\.0\. We recommend TLS 1\.2 or above\. Clients must also support cipher suites with Perfect Forward Secrecy \(PFS\), such as Ephemeral Diffie\-Hellman \(DHE\) or Elliptic Curve Diffie\-Hellman Ephemeral \(ECDHE\)\. Most modern systems such as Java 7 and later support these modes\. Additionally, you must sign requests using an access key ID and a secret access key that are associated with an IAM principal, or you can use the [AWS Security Token Service \(STS\)](https://docs.aws.amazon.com/STS/latest/APIReference/Welcome.html) to generate temporary security credentials to sign requests\.

## Traffic between AWS resources in the same Region<a name="inter-network-traffic-privacy-within-region"></a>

An Amazon Virtual Private Cloud \(Amazon VPC\) endpoint for DynamoDB is a logical entity within a VPC that allows connectivity only to DynamoDB\. The Amazon VPC routes requests to DynamoDB and routes responses back to the VPC\. For more information, see [VPC endpoints](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints.html) in the *Amazon VPC User Guide*\. For example policies that you can use to control access from VPC endpoints, see [Using IAM policies to control access to DynamoDB](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints-ddb.html)\. 

**Note**  
Amazon VPC endpoints are not accessible via AWS Site\-to\-Site VPN or AWS Direct Connect\.