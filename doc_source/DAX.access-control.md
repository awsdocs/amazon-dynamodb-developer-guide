# DAX access control<a name="DAX.access-control"></a>

DynamoDB Accelerator \(DAX\) is designed to work together with DynamoDB, to seamlessly add a caching layer to your applications\. However, DAX and DynamoDB have separate access control mechanisms\. Both services use AWS Identity and Access Management \(IAM\) to implement their respective security policies, but the security models for DAX and DynamoDB are different\.

*We highly recommend that you understand both security models*, so that you can implement proper security measures for your applications that use DAX\.

This section describes the access control mechanisms provided by DAX and provides sample IAM policies that you can tailor to your needs\. 

With DynamoDB, you can create IAM policies that limit the actions a user can perform on individual DynamoDB resources\. For example, you can create a user role that only allows the user to perform read\-only actions on a particular DynamoDB table\. \(For more information, see  [Identity and Access Management for Amazon DynamoDB](security-iam.md)\.\) By comparison, the DAX security model focuses on cluster security, and the ability of the cluster to perform DynamoDB API actions on your behalf\.

**Warning**  
If you are currently using IAM roles and policies to restrict access to DynamoDB tables data, then the use of DAX can **subvert** those policies\. For example, a user could have access to a DynamoDB table via DAX but not have explicit access to the same table accessing DynamoDB directly\. For more information, see [Identity and Access Management for Amazon DynamoDB](security-iam.md)\.  
DAX does not enforce user\-level separation on data in DynamoDB\. Instead, users inherit the permissions of the DAX cluster's IAM policy when they access that cluster\. Thus, when accessing DynamoDB tables via DAX, the only access controls that are in effect are the permissions in the DAX cluster's IAM policy\. No other permissions are recognized\.  
If you require isolation, we recommend that you create additional DAX clusters and scope the IAM policy for each cluster accordingly\. For example, you could create multiple DAX clusters and allow each cluster to access only a single table\.

## IAM service role for DAX<a name="DAX.access-control.iam-service-role"></a>

When you create a DAX cluster, you must associate the cluster with an IAM role\. This is known as the *service role* for the cluster\. 

Suppose that you wanted to create a new DAX cluster named *DAXCluster01*\. You could create a service role named *DAXServiceRole*, and associate the role with *DAXCluster01*\. The policy for *DAXServiceRole* would define the DynamoDB actions that *DAXCluster01* could perform, on behalf of the users who interact with *DAXCluster01*\.

When you create a service role, you must specify a trust relationship between *DAXServiceRole* and the DAX service itself\. A trust relationship determines which entities can assume a role and make use of its permissions\. The following is an example trust relationship document for *DAXServiceRole*: 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Service": "dax.amazonaws.com"
            },
            "Action": "sts:AssumeRole"
        }
    ]
}
```

This trust relationship allows a DAX cluster to assume *DAXServiceRole* and perform DynamoDB API calls on your behalf\. 

The DynamoDB API actions that are allowed are described in an IAM policy document, which you attach to *DAXServiceRole*\. The following is an example policy document\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "DaxAccessPolicy",
            "Effect": "Allow",
            "Action": [
                "dynamodb:DescribeTable",
                "dynamodb:PutItem",
                "dynamodb:GetItem",
                "dynamodb:UpdateItem",
                "dynamodb:DeleteItem",
                "dynamodb:Query",
                "dynamodb:Scan",
                "dynamodb:BatchGetItem",
                "dynamodb:BatchWriteItem",
                "dynamodb:ConditionCheckItem"
            ],
            "Resource": [
                "arn:aws:dynamodb:us-west-2:123456789012:table/Books"
            ]
        }
    ]
}
```

This policy allows DAX to perform necessary DynamoDB API actions on a DynamoDB table\. The `dynamodb:DescribeTable` action is required for DAX to maintain metadata about the table, and the others are read and write actions performed on items in the table\. The table, named `Books`, is in the us\-west\-2 Region and is owned by AWS account ID `123456789012`\.

## IAM policy to allow DAX cluster access<a name="DAX.access-control.iam-allow-dax-cluster-access"></a>

After you create a DAX cluster, you need to grant permissions to a user so that the user can access the DAX cluster\.

For example, suppose that you want to grant access to *DAXCluster01* to a user named Alice\. You would first create an IAM policy \(*AliceAccessPolicy*\) that defines the DAX clusters and DAX API actions that the recipient can access\. You would then confer access by attaching this policy to user Alice\.

The following policy document gives the recipient full access on *DAXCluster01*\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "dax:*"
            ],
            "Effect": "Allow",
            "Resource": [
                "arn:aws:dax:us-west-2:123456789012:cache/DAXCluster01"
            ]
        }
    ]
}
```

The policy document allows access to the DAX cluster, but it does not grant any DynamoDB permissions\. \(The DynamoDB permissions are conferred by the DAX service role\.\)

For user Alice, you would first create `AliceAccessPolicy` with the policy document shown previously\. You would then attach the policy to Alice\.

**Note**  
Instead of attaching the policy to a user, you could attach it to an IAM role\. That way, all of the users who assume that role would have the permissions that you defined in the policy\.

The user policy, together with the DAX service role, determine the DynamoDB resources and API actions that the recipient can access via DAX\.

## Case study: Accessing DynamoDB and DAX<a name="DAX.access-control.case-study"></a>

The following scenario can help further your understanding of IAM policies for use with DAX\. \(This scenario is referred to throughout the rest of this section\.\) The following diagram shows a high\-level overview of the scenario\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/dax-access-control-scenario.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

In this scenario, there are the following entities:
+ A user \(Bob\)\. 
+ An IAM role \(`BobUserRole`\)\. Bob assumes this role at runtime\.
+ An IAM policy \(`BobAccessPolicy`\)\. This policy is attached to `BobUserRole`\. `BobAccessPolicy` defines the DynamoDB and DAX resources that `BobUserRole` is allowed to access\.
+ A DAX cluster \(`DAXCluster01`\)\.
+ An IAM service role \(`DAXServiceRole`\)\. This role allows `DAXCluster01` to access DynamoDB\. 
+ An IAM policy \(`DAXAccessPolicy`\)\. This policy is attached to `DAXServiceRole`\. `DAXAccessPolicy` defines the DynamoDB APIs and resources that `DAXCluster01` is allowed to access\.
+ A DynamoDB table \(`Books`\)\.

The combination of policy statements in `BobAccessPolicy` and `DAXAccessPolicy` determine what Bob can do with the `Books` table\. For example, Bob might be able to access `Books` directly \(using the DynamoDB endpoint\), indirectly \(using the DAX cluster\), or both\. Bob might also be able to read data from `Books`, write data to `Books`, or both\.

## Access to DynamoDB, but no access with DAX<a name="DAX.access-control.ddb-yes-dax-no"></a>

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/dax-access-control-ddb-only.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

It is possible to allow direct access to a DynamoDB table, while preventing indirect access using a DAX cluster\. For direct access to DynamoDB, the permissions for `BobUserRole` are determined by `BobAccessPolicy` \(which is attached to the role\)\.

### Read\-only access to DynamoDB \(only\)<a name="DAX.access-control.ddb-yes-dax-no.ddb-read-only"></a>

*Bob* can access DynamoDB with `BobUserRole`\. The IAM policy attached to this role \(`BobAccessPolicy`\) determines the DynamoDB tables that `BobUserRole` can access, and what APIs that `BobUserRole` can invoke\. 

Consider the following policy document for `BobAccessPolicy`\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "DynamoDBAccessStmt",
            "Effect": "Allow",
            "Action": [
                "dynamodb:GetItem",
                "dynamodb:BatchGetItem",
                "dynamodb:Query",
                "dynamodb:Scan"
            ],
            "Resource": "arn:aws:dynamodb:us-west-2:123456789012:table/Books"
        }
    ]
}
```

When this document is attached to `BobAccessPolicy`, it allows `BobUserRole` to access the DynamoDB endpoint and perform read\-only operations on the `Books` table\.

DAX does not appear in this policy, so access via DAX is denied\.

### Read/write access to DynamoDB \(only\)<a name="DAX.access-control.ddb-yes-dax-no.ddb-read-write"></a>

If `BobUserRole` requires read/write access to DynamoDB, the following policy would work\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "DynamoDBAccessStmt",
            "Effect": "Allow",
            "Action": [
                "dynamodb:GetItem",
                "dynamodb:BatchGetItem",
                "dynamodb:Query",
                "dynamodb:Scan",
                "dynamodb:PutItem",
                "dynamodb:UpdateItem",
                "dynamodb:DeleteItem",
                "dynamodb:BatchWriteItem",
                "dynamodb:ConditionCheckItem"
            ],
            "Resource": "arn:aws:dynamodb:us-west-2:123456789012:table/Books"
        }
    ]
}
```

Again, DAX does not appear in this policy, so access via DAX is denied\.

## Access to DynamoDB and to DAX<a name="DAX.access-control.ddb-yes-dax-yes"></a>

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/dax-access-control-ddb-and-dax.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

To allow access to a DAX cluster, you must include DAX\-specific actions in an IAM policy\.

The following DAX\-specific actions correspond to their similarly named counterparts in the DynamoDB API:
+ `dax:GetItem`
+ `dax:BatchGetItem`
+ `dax:Query`
+ `dax:Scan`
+ `dax:PutItem`
+ `dax:UpdateItem`
+ `dax:DeleteItem`
+ `dax:BatchWriteItem`
+ `dax:ConditionCheckItem`

The same is true for the `dax:EnclosingOperation` condition key\.

### Read\-only access to DynamoDB and read\-only access to DAX<a name="DAX.access-control.ddb-yes-dax-yes.ddb-read-only-dax-read-only"></a>

Suppose that Bob requires read\-only access to the `Books` table, from DynamoDB and from DAX\. The following policy \(attached to `BobUserRole`\) confers this access\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "DAXAccessStmt",
            "Effect": "Allow",
            "Action": [
                "dax:GetItem",
                "dax:BatchGetItem",
                "dax:Query",
                "dax:Scan"
            ],
            "Resource": "arn:aws:dax:us-west-2:123456789012:cache/DAXCluster01"
        },
        {
            "Sid": "DynamoDBAccessStmt",
            "Effect": "Allow",
            "Action": [
                "dynamodb:GetItem",
                "dynamodb:BatchGetItem",
                "dynamodb:Query",
                "dynamodb:Scan"
            ],
            "Resource": "arn:aws:dynamodb:us-west-2:123456789012:table/Books"
        }
    ]
}
```

The policy has a statement for DAX access \(`DAXAccessStmt`\) and another statement for DynamoDBaccess \(`DynamoDBAccessStmt`\)\. These statements allow Bob to send `GetItem`, `BatchGetItem`, `Query`, and `Scan` requests to `DAXCluster01`\.

However, the service role for `DAXCluster01` would also require read\-only access to the `Books` table in DynamoDB\. The following IAM policy, attached to `DAXServiceRole`, would fulfill this requirement\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "DynamoDBAccessStmt",
            "Effect": "Allow",
            "Action": [
                "dynamodb:GetItem",
                "dynamodb:BatchGetItem",
                "dynamodb:Query",
                "dynamodb:Scan"
            ],
            "Resource": "arn:aws:dynamodb:us-west-2:123456789012:table/Books"
        }
    ]
}
```

### Read/write access to DynamoDB and read\-only with DAX<a name="DAX.access-control.ddb-yes-dax-yes.ddb-read-write-dax-read-only"></a>

For a given user role, you can provide read/write access to a DynamoDB table, while also allowing read\-only access via DAX\. 

For Bob, the IAM policy for `BobUserRole` would need to allow DynamoDB read and write actions on the `Books` table, while also supporting read\-only actions via `DAXCluster01`\.

The following example policy document for `BobUserRole` confers this access\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "DAXAccessStmt",
            "Effect": "Allow",
            "Action": [
                "dax:GetItem",
                "dax:BatchGetItem",
                "dax:Query",
                "dax:Scan"
            ],
            "Resource": "arn:aws:dax:us-west-2:123456789012:cache/DAXCluster01"
        },
        {
            "Sid": "DynamoDBAccessStmt",
            "Effect": "Allow",
            "Action": [
                "dynamodb:GetItem",
                "dynamodb:BatchGetItem",
                "dynamodb:Query",
                "dynamodb:Scan",
                "dynamodb:PutItem",
                "dynamodb:UpdateItem",
                "dynamodb:DeleteItem",
                "dynamodb:BatchWriteItem",
                "dynamodb:DescribeTable",
                "dynamodb:ConditionCheckItem"
            ],
            "Resource": "arn:aws:dynamodb:us-west-2:123456789012:table/Books"
        }
    ]
}
```

In addition, `DAXServiceRole` would require an IAM policy that allows `DAXCluster01` to perform read\-only actions on the `Books` table\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "DynamoDBAccessStmt",
            "Effect": "Allow",
            "Action": [
                "dynamodb:GetItem",
                "dynamodb:BatchGetItem",
                "dynamodb:Query",
                "dynamodb:Scan",
                "dynamodb:DescribeTable"
           ],
            "Resource": "arn:aws:dynamodb:us-west-2:123456789012:table/Books"
        }
    ]
}
```

### Read/write access to DynamoDB and read/write access to DAX<a name="DAX.access-control.ddb-yes-dax-yes.ddb-read-write-dax-read-write.title"></a>

Now suppose that Bob required read/write access to the `Books` table, directly from DynamoDB or indirectly from `DAXCluster01`\. The following policy document, attached to `BobAccessPolicy`, confers this access\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "DAXAccessStmt",
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
            "Resource": "arn:aws:dax:us-west-2:123456789012:cache/DAXCluster01"
        },
        {
            "Sid": "DynamoDBAccessStmt",
            "Effect": "Allow",
            "Action": [
                "dynamodb:GetItem",
                "dynamodb:BatchGetItem",
                "dynamodb:Query",
                "dynamodb:Scan",
                "dynamodb:PutItem",
                "dynamodb:UpdateItem",
                "dynamodb:DeleteItem",
                "dynamodb:BatchWriteItem",
                "dynamodb:DescribeTable",
                "dynamodb:ConditionCheckItem"
            ],
            "Resource": "arn:aws:dynamodb:us-west-2:123456789012:table/Books"
        }
    ]
}
```

In addition, `DAXServiceRole` would require an IAM policy that allows `DAXCluster01` to perform read/write actions on the `Books` table\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "DynamoDBAccessStmt",
            "Effect": "Allow",
            "Action": [
                "dynamodb:GetItem",
                "dynamodb:BatchGetItem",
                "dynamodb:Query",
                "dynamodb:Scan",
                "dynamodb:PutItem",
                "dynamodb:UpdateItem",
                "dynamodb:DeleteItem",
                "dynamodb:BatchWriteItem",
                "dynamodb:DescribeTable"
            ],
            "Resource": "arn:aws:dynamodb:us-west-2:123456789012:table/Books"
        }
    ]
}
```

## Access to DynamoDB via DAX, but no direct access to DynamoDB<a name="DAX.access-control.ddb-no-dax-yes.ddb-read-write-dax-read-write"></a>

 In this scenario, Bob can access the `Books` table via DAX, but he does not have direct access to the `Books` table in DynamoDB\. Thus, when Bob gains access to DAX, he also gains access to a DynamoDB table that he otherwise might not be able to access\. When you configure an IAM policy for the DAX service role, remember that any user that is given access to the DAX cluster via the user access policy gains access to the tables specified in that policy\. In this case, `BobAccessPolicy` gains access to the tables specified in `DAXAccessPolicy`\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/dax-access-control-dax-only.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

If you are currently using IAM roles and policies to restrict access to DynamoDB tables and data, using DAX can subvert those policies\. In the following policy, Bob has access to a DynamoDB table via DAX but does not have explicit direct access to the same table in DynamoDB\. 

 The following policy document \(`BobAccessPolicy`\), attached to `BobUserRole`, would confer this access\. 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "DAXAccessStmt",
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
            "Resource": "arn:aws:dax:us-west-2:123456789012:cache/DAXCluster01"
        }
    ]
}
```

In this access policy, there are no permissions to access DynamoDB directly\. 

Together with `BobAccessPolicy`, the following `DAXAccessPolicy` gives `BobUserRole` access to the DynamoDB table `Books` even though `BobUserRole` cannot directly access the `Books` table\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "DynamoDBAccessStmt",
            "Effect": "Allow",
            "Action": [
                "dynamodb:GetItem",
                "dynamodb:BatchGetItem",
                "dynamodb:Query",
                "dynamodb:Scan",
                "dynamodb:PutItem",
                "dynamodb:UpdateItem",
                "dynamodb:DeleteItem",
                "dynamodb:BatchWriteItem",
                "dynamodb:DescribeTable",
                "dynamodb:ConditionCheckItem"
            ],
            "Resource": "arn:aws:dynamodb:us-west-2:123456789012:table/Books"
        }
    ]
}
```

As this example shows, when you configure access control for the user access policy and the DAX cluster access policy, you must fully understand the end\-to\-end access to ensure that the principle of least privilege is observed\. Also ensure that giving a user access to a DAX cluster does not subvert previously established access control policies\.