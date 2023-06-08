# DAX encryption at rest<a name="DAXEncryptionAtRest"></a>

Amazon DynamoDB Accelerator \(DAX\) encryption at rest provides an additional layer of data protection by helping secure your data from unauthorized access to the underlying storage\. Organizational policies, industry or government regulations, and compliance requirements might require the use of encryption at rest to protect your data\. You can use encryption to increase the data security of your applications that are deployed in the cloud\. 

With encryption at rest, the data persisted by DAX on disk is encrypted using 256\-bit Advanced Encryption Standard, also known as AES\-256 encryption\. DAX writes data to disk as part of propagating changes from the primary node to read replicas\. 

DAX encryption at rest automatically integrates with AWS Key Management Service \(AWS KMS\) for managing the single service default key that is used to encrypt your clusters\. If a service default key doesn't exist when you create your encrypted DAX cluster, AWS KMS automatically creates a new AWS managed key for you\. This key is used with encrypted clusters that are created in the future\. AWS KMS combines secure, highly available hardware and software to provide a key management system scaled for the cloud\. 

After your data is encrypted, DAX handles the decryption of your data transparently with minimal impact on performance\. You don't need to modify your applications to use encryption\. 

**Note**  
DAX does not call AWS KMS for every single DAX operation\. DAX only uses the key at the cluster launch\. Even if access is revoked, DAX can still access the data until the cluster is shut down\. Customer\-specified AWS KMS keys are not supported\. 

DAX encryption at rest is available for the following cluster node types\.


| Family | Node type | 
| --- | --- | 
| Memory\-optimized \(R4 and R5\) |  dax\.r4\.large dax\.r4\.xlarge dax\.r4\.2xlarge dax\.r4\.4xlarge dax\.r4\.8xlarge dax\.r4\.16xlarge dax\.r5\.large dax\.r5\.xlarge dax\.r5\.2xlarge dax\.r5\.4xlarge dax\.r5\.8xlarge dax\.r5\.12xlarge dax\.r5\.16xlarge dax\.r5\.24xlarge  | 
| General purpose \(T2\) |  dax\.t2\.small dax\.t2\.medium  | 
| General purpose \(T3\) |  dax\.t3\.small dax\.t3\.medium  | 

**Important**  
DAX encryption at rest is not supported for `dax.r3.*` node types\. 

You cannot enable or disable encryption at rest after a cluster has been created\. You must re\-create the cluster to enable encryption at rest if it was not enabled at creation\. 

DAX encryption at rest is offered at no additional cost \(AWS KMS encryption key usage charges apply\)\. For information about pricing, see [Amazon DynamoDB pricing](https://aws.amazon.com/dynamodb/pricing)\.

## Enabling encryption at rest using the AWS Management Console<a name="dax.encryption.tutorial-console"></a>

Follow these steps to enable DAX encryption at rest on a table using the console\.

**To enable DAX encryption at rest**

1. Sign in to the AWS Management Console and open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1.  In the navigation pane on the left side of the console, under **DAX**, choose **Clusters**\.

1.  Choose **Create cluster**\.

1. For **Cluster name**, enter a short name for your cluster\. Choose the **node type** for all of the nodes in the cluster, and for the cluster size, use **3** nodes\.

1. In **Encryption**, make sure that **Enable encryption** is selected\.  
![\[Screenshot of cluster settings in the console showing the enable encryption setting.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/dax_encrypt.PNG)

1. After choosing the IAM role, subnet group, security groups, and cluster settings, choose **Launch cluster**\. 

To confirm that the cluster is encrypted, check the cluster details under the **Clusters** pane\. Encryption should be **ENABLED**\.