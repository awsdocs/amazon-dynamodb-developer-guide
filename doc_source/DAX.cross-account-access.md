# Accessing DAX across AWS accounts<a name="DAX.cross-account-access"></a>

Imagine that you have a DynamoDB Accelerator \(DAX\) cluster running in one AWS account \(account A\), and the DAX cluster needs to be accessible from an Amazon Elastic Compute Cloud \(Amazon EC2\) instance in another AWS account \(account B\)\. In this tutorial, you accomplish this by launching an EC2 instance in account B with an IAM role from account B\. You then use temporary security credentials from the EC2 instance to assume an IAM role from account A\. Finally, you use the temporary security credentials from assuming the IAM role in account A to make application calls over an Amazon VPC peering connection to the DAX cluster in account A\. In order to perform these tasks you will need administrative access in both AWS accounts\.

**Topics**
+ [Set up IAM](#DAX.cross-account-access.iam-setup)
+ [Set up a VPC](#DAX.cross-account-access.vpc-setup)
+ [Modify the DAX client to allow cross\-account access](#DAX.cross-account-access.modify-client)

## Set up IAM<a name="DAX.cross-account-access.iam-setup"></a>

1. Create a text file named `AssumeDaxRoleTrust.json` with the following content, which allows Amazon EC2 to work on your behalf\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Principal": {
                   "Service": "ec2.amazonaws.com"
               },
               "Action": "sts:AssumeRole"
           }
       ]
   }
   ```

1. In account B, create a role that Amazon EC2 can use when launching instances\.

   ```
   aws iam create-role \
       --role-name AssumeDaxRole \
       --assume-role-policy-document file://AssumeDaxRoleTrust.json
   ```

1. Create a text file named `AssumeDaxRolePolicy.json` with the following content, which allows code running on the EC2 instance in account B to assume an IAM role in account A\. Replace *accountA* with the actual ID of account A\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": "sts:AssumeRole",
               "Resource": "arn:aws:iam::accountA:role/DaxCrossAccountRole"
           }
       ]
   }
   ```

1. Add that policy to the role you just created\.

   ```
   aws iam put-role-policy \
       --role-name AssumeDaxRole \
       --policy-name AssumeDaxRolePolicy \
       --policy-document file://AssumeDaxRolePolicy.json
   ```

1. Create an instance profile to allow instances to use the role\.

   ```
   aws iam create-instance-profile \
       --instance-profile-name AssumeDaxInstanceProfile
   ```

1. Associate the role with the instance profile\.

   ```
   aws iam add-role-to-instance-profile \
       --instance-profile-name AssumeDaxInstanceProfile \
       --role-name AssumeDaxRole
   ```

1. Create a text file named `DaxCrossAccountRoleTrust.json` with the following content, which allows account B to assume an account A role\. Replace *accountB* with the actual ID of account B\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Principal": {
                   "AWS": "arn:aws:iam::accountB:role/AssumeDaxRole"
               },
               "Action": "sts:AssumeRole"
           }
       ]
   }
   ```

1. In account A, create the role that account B can assume\.

   ```
   aws iam create-role \
       --role-name DaxCrossAccountRole \
       --assume-role-policy-document file://DaxCrossAccountRoleTrust.json
   ```

1. Create a text file named `DaxCrossAccountPolicy.json` that allows access to the DAX cluster\. Replace *dax\-cluster\-arn* with the correct Amazon Resource Name \(ARN\) of your DAX cluster\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": [
                   "dax:GetItem",
                   "dax:BatchGetItem",
                   "dax:Query",
                   "dax:Scan",
                   "dax:PutItem",
                   "dax:UpdateItem",
                   "dax:DeleteItem",
                   "dax:BatchWriteItem",
                   "dax:ConditionCheckItem"
               ],
               "Resource": "dax-cluster-arn"
           }
       ]
   }
   ```

1. In account A, add the policy to the role\.

   ```
   aws iam put-role-policy \
       --role-name DaxCrossAccountRole \
       --policy-name DaxCrossAccountPolicy \
       --policy-document file://DaxCrossAccountPolicy.json
   ```

## Set up a VPC<a name="DAX.cross-account-access.vpc-setup"></a>

1. Find the subnet group of account A's DAX cluster\. Replace *cluster\-name* with the name of the DAX cluster that account B must access\.

   ```
   aws dax describe-clusters \
       --cluster-name cluster-name
       --query 'Clusters[0].SubnetGroup'
   ```

1. Using that *subnet\-group*, find the cluster's VPC\.

   ```
   aws dax describe-subnet-groups \
       --subnet-group-name subnet-group \
       --query 'SubnetGroups[0].VpcId'
   ```

1. Using that *vpc\-id*, find the VPC's CIDR\.

   ```
   aws ec2 describe-vpcs \
       --vpc vpc-id \
       --query 'Vpcs[0].CidrBlock'
   ```

1. From account B, create a VPC using a different, non\-overlapping CIDR than the one found in the previous step\. Then, create at least one subnet\. You can use either the [VPC creation wizard](https://docs.aws.amazon.com/vpc/latest/userguide/getting-started-ipv4.html#getting-started-create-vpc) in the AWS Management Console or the [AWS CLI](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-vpc.html)\.

1. From account B, request a peering connection to the account A VPC as described in [Creating and accepting a VPC peering connection](https://docs.aws.amazon.com/vpc/latest/peering/create-vpc-peering-connection.html)\. From account A, accept the connection\.

1. From account B, find the new VPC's routing table\. Replace *vpc\-id* with the ID of the VPC you created in account B\.

   ```
   aws ec2 describe-route-tables \
       --filters 'Name=vpc-id,Values=vpc-id' \
       --query 'RouteTables[0].RouteTableId'
   ```

1. Add a route to send traffic destined for account A's CIDR to the VPC peering connection\. Remember to replace each *user input placeholder* with the correct values for your accounts\.

   ```
   aws ec2 create-route \
       --route-table-id accountB-route-table-id \
       --destination-cidr accountA-vpc-cidr \
       --vpc-peering-connection-id peering-connection-id
   ```

1. From account A, find the DAX cluster's route table using the *vpc\-id* you found previously\.

   ```
   aws ec2 describe-route-tables \
       --filters 'Name=vpc-id, Values=accountA-vpc-id' \
       --query 'RouteTables[0].RouteTableId'
   ```

1. From account A, add a route to send traffic destined for account B's CIDR to the VPC peering connection\. Replace each *user input placeholder* with the correct values for your accounts\.

   ```
   aws ec2 create-route \
       --route-table-id accountA-route-table-id \
       --destination-cidr accountB-vpc-cidr \
       --vpc-peering-connection-id peering-connection-id
   ```

1. From account B, launch an EC2 instance in the VPC that you created earlier\. Give it the `AssumeDaxInstanceProfile`\. You can use either the [launch wizard](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/launching-instance.html) in the AWS Management Console or the [AWS CLI](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html)\. Take note of the instance's security group\.

1. From account A, find the security group used by the DAX cluster\. Remember to replace *cluster\-name* with the name of your DAX cluster\.

   ```
   aws dax describe-clusters \
       --cluster-name cluster-name \
       --query 'Clusters[0].SecurityGroups[0].SecurityGroupIdentifier'
   ```

1. Update the DAX cluster's security group to allow inbound traffic from the security group of the EC2 instance you created in account B\. Remember to replace the *user input placeholders* with the correct values for your accounts\.

   ```
   aws ec2 authorize-security-group-ingress \
       --group-id accountA-security-group-id \
       --protocol tcp \
       --port 8111 \
       --source-group accountB-security-group-id \
       --group-owner accountB-id
   ```

At this point, an application on account B's EC2 instance is able to use the instance profile to assume the `arn:aws:iam::accountA-id:role/DaxCrossAccountRole` role and use the DAX cluster\.

## Modify the DAX client to allow cross\-account access<a name="DAX.cross-account-access.modify-client"></a>

**Note**  
AWS Security Token Service \(AWS STS\) credentials are temporary credentials\. Some clients handle refreshing automatically, while others require additional logic to refresh the credentials\. We recommend that you follow the guidance of the appropriate documentation\.

------
#### [ Java ]

This section helps you modify your existing DAX client code to allow cross\-account DAX access\. If you don't have DAX client code already, you can find working code examples in the [Java and DAX](DAX.client.run-application-java.md) tutorial\.

1. Add the following imports\.

   ```
   import com.amazonaws.auth.STSAssumeRoleSessionCredentialsProvider;
   import com.amazonaws.services.securitytoken.AWSSecurityTokenService;
   import com.amazonaws.services.securitytoken.AWSSecurityTokenServiceClientBuilder;
   ```

1. Get a credentials provider from AWS STS and create a DAX client object\. Remember to replace each *user input placeholder* with the correct values for your accounts\.

   ```
   AWSSecurityTokenService awsSecurityTokenService = AWSSecurityTokenServiceClientBuilder
        .standard()
        .withRegion(region)
        .build();
   
   STSAssumeRoleSessionCredentialsProvider credentials =  new STSAssumeRoleSessionCredentialsProvider.Builder("arn:aws:iam::accountA:role/RoleName", "TryDax")
        .withStsClient(awsSecurityTokenService)
        .build();
   
   DynamoDB client = AmazonDaxClientBuilder.standard()
       .withRegion(region)
       .withEndpointConfiguration(dax_endpoint)
       .withCredentials(credentials)
       .build();
   ```

------
#### [ \.NET ]

This section helps you modify your existing DAX client code to allow cross\-account DAX access\. If you don't have DAX client code already, you can find working code examples in the [\.NET and DAX](DAX.client.run-application-dotnet.md) tutorial\.

1. Add the [AWSSDK\.SecurityToken](https://www.nuget.org/packages/AWSSDK.SecurityToken) NuGet package to the solution\.

   ```
   <PackageReference Include="AWSSDK.SecurityToken" Version="latest version" />
   ```

1. Use the `SecurityToken` and `SecurityToken.Model` packages\.

   ```
   using Amazon.SecurityToken;
   using Amazon.SecurityToken.Model;
   ```

1. Get temporary credentials from `AmazonSimpleTokenService` and create a `ClusterDaxClient` object\. Remember to replace each *user input placeholder* with the correct values for your accounts\.

   ```
   IAmazonSecurityTokenService sts = new AmazonSecurityTokenServiceClient();
   
   var assumeRoleResponse = sts.AssumeRole(new AssumeRoleRequest
   {
       RoleArn = "arn:aws:iam::accountA:role/RoleName",
                   RoleSessionName = "TryDax"
   });
   
   Credentials credentials = assumeRoleResponse.Credentials;
   
   var clientConfig = new DaxClientConfig(dax_endpoint, port)
   {
       AwsCredentials = assumeRoleResponse.Credentials
                      
   };
   
   var client = new ClusterDaxClient(clientConfig);
   ```

------
#### [ Go ]

This section helps you modify your existing DAX client code to allow cross\-account DAX access\. If you don't have DAX client code already, you can find [working code examples on GitHub](https://github.com/aws-samples/aws-dax-go-sample/blob/master/try_dax.go)\.

1. Import the AWS STS and session packages\.

   ```
   import (
       "github.com/aws/aws-sdk-go/aws/session"
       "github.com/aws/aws-sdk-go/service/sts"
       "github.com/aws/aws-sdk-go/aws/credentials/stscreds"
   )
   ```

1. Get temporary credentials from `AmazonSimpleTokenService` and create a DAX client object\. Remember to replace each *user input placeholder* with the correct values for your accounts\.

   ```
   sess, err := session.NewSession(&aws.Config{
       Region: aws.String(region)},
   )
   if err != nil {
       return nil, err
   }
   
   stsClient := sts.New(sess)
   arp := &stscreds.AssumeRoleProvider{
                   Duration:     900 * time.Second,
                   ExpiryWindow: 10 * time.Second,
                   RoleARN:      "arn:aws:iam::accountA:role/role_name",
                   Client:       stsClient,
                   RoleSessionName: "session_name",
           }cfg := dax.DefaultConfig()
   
   cfg.HostPorts = []string{dax_endpoint}
   cfg.Region = region
   cfg.Credentials = credentials.NewCredentials(arp)
   daxClient := dax.New(cfg)
   ```

------
#### [ Python ]

This section helps you modify your existing DAX client code to allow cross\-account DAX access\. If you don't have DAX client code already, you can find working code examples in the [Python and DAX](DAX.client.run-application-python.md) tutorial\.

1. Import `boto3`\.

   ```
   import boto3
   ```

1. Get temporary credentials from `sts` and create an `AmazonDaxClient` object\. Remember to replace each *user input placeholder* with the correct values for your accounts\.

   ```
   sts = boto3.client('sts')
   stsresponse = sts.assume_role(RoleArn='arn:aws:iam::accountA:role/RoleName',RoleSessionName='tryDax')
   credentials = botocore.session.get_session()['Credentials']
   
   dax = amazondax.AmazonDaxClient(session, region_name=region, endpoints=[dax_endpoint], aws_access_key_id=credentials['AccessKeyId'], aws_secret_access_key=credentials['SecretAccessKey'], aws_session_token=credentials['SessionToken'])
   client = dax
   ```

------
#### [ Node\.js ]

This section helps you modify your existing DAX client code to allow cross\-account DAX access\. If you don't have DAX client code already, you can find working code examples in the [Node\.js and DAX](DAX.client.run-application-nodejs.md) tutorial\. Remember to replace each *user input placeholder* with the correct values for your accounts\.

```
const AmazonDaxClient = require('amazon-dax-client');
const AWS = require('aws-sdk');
const region = 'region';
const endpoints = [daxEndpoint1, ...];

const getCredentials = async() => {
  return new Promise((resolve, reject) => {
    const sts = new AWS.STS();
    const roleParams = {
      RoleArn: 'arn:aws:iam::accountA:role/RoleName',
      RoleSessionName: 'tryDax',
    };
    sts.assumeRole(roleParams, (err, session) => {
      if(err) {
        reject(err);
      } else {
        resolve({
          accessKeyId: session.Credentials.AccessKeyId,
          secretAccessKey: session.Credentials.SecretAccessKey,
          sessionToken: session.Credentials.SessionToken,
        });
      }
    });
  });
};

const createDaxClient = async() => {
  const credentials = await getCredentials();
  const daxClient = new AmazonDaxClient({endpoints: endpoints, region: region, accessKeyId: credentials.accessKeyId, secretAccessKey: credentials.secretAccessKey, sessionToken: credentials.sessionToken});
  return new AWS.DynamoDB.DocumentClient({service: daxClient});
};

createDaxClient().then((client) => {
  client.get(...);
  ...
}).catch((error) => {
  console.log('Caught an error: ' + error);
});
```

------