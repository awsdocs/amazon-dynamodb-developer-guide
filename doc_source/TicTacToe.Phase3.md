# Step 3: Deploy in Production Using the DynamoDB Service<a name="TicTacToe.Phase3"></a>

**Topics**
+ [3\.1: Create an IAM Role for Amazon EC2](#TicTacToe.DeployInProd.IAMCreateRole)
+ [3\.2: Create the Games Table in Amazon DynamoDB](#TicTacToe.DeployInProd.CreateTable)
+ [3\.3: Bundle and Deploy Tic\-Tac\-Toe Application Code](#TicTacToe.DeployInProd.IAMBundleDeployCode)
+ [3\.4: Set Up the AWS Elastic Beanstalk Environment](#TicTacToe.DeployInProd.SetUpElasticBeanstalk)

In the preceding sections, you deployed and tested the Tic\-Tac\-Toe application locally on your computer using DynamoDB Local\. Now, you deploy the application in production as follows:
+ Deploy the application using Elastic Beanstalk, an easy\-to\-use service for deploying and scaling web applications and services\. For more information, go to [ Deploying a Flask Application to AWS Elastic Beanstalk](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/create-deploy-python-flask.html)\.

  Elastic Beanstalk will launch one or more Amazon Elastic Compute Cloud \(Amazon EC2\) instances, which you configure through Elastic Beanstalk, on which your Tic\-Tac\-Toe application will run\.
+ Using the Amazon DynamoDB service, create a Games table that exists on AWS rather than locally on your computer\. 

In addition, you also have to configure permissions\. Any AWS resources you create, such as the Games table in DynamoDB, are private by default\. Only the resource owner, that is the AWS account that created the Games table, can access this table\. Thus, by default your Tic\-Tac\-Toe application cannot update the Games table\. 

To grant necessary permissions, you will create an AWS Identity and Access Management \(IAM\) role and grant this role permissions to access the Games table\. Your Amazon EC2 instance first assumes this role\. In response, AWS returns temporary security credentials that the Amazon EC2 instance can use to update the Games table on behalf of the Tic\-Tac\-Toe application\. When you configure your Elastic Beanstalk application, you specify the IAM role that the Amazon EC2 instance or instances can assume\. For more information about IAM roles, go to [IAM Roles for Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html) in the *Amazon EC2 User Guide for Linux Instances\. *

**Note**  
Before you create Amazon EC2 instances for the Tic\-Tac\-Toe application, you must first decide the AWS region where you want Elastic Beanstalk to create the instances\. After you create the Elastic Beanstalk application, you provide the same region name and endpoint in a configuration file\. The Tic\-Tac\-Toe application uses information in this file to create the Games table and send subsequent requests in a specific AWS region\. Both the DynamoDB Games table and the Amazon EC2 instances that Elastic Beanstalk launches must be in the same AWS region\. For a list of available regions, go to [Amazon DynamoDB ](https://docs.aws.amazon.com/general/latest/gr/rande.html#ddb_region) in the *Amazon Web Services General Reference*\.

In summary, you do the following to deploy the Tic\-Tac\-Toe application in production:

1. Create an IAM role using the AWS IAM service\. You will attach a policy to this role granting permissions for DynamoDB actions to access the Games table\. 

1. Bundle the Tic\-Tac\-Toe application code and a configuration file, and create a `.zip` file\. You use this `.zip` file to give the Tic\-Tac\-Toe application code to Elastic Beanstalk to put on your servers\. For more information on creating a bundle, go to [Creating an Application Source Bundle](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.deployment.source.html) in the *AWS Elastic Beanstalk Developer Guide*\.

   In the configuration file \(`beanstalk.config`\), you provide AWS region and endpoint information\. The Tic\-Tac\-Toe application uses this information to determine which DynamoDB region to talk to\.  

1. Set up the Elastic Beanstalk environment\. Elastic Beanstalk will launch an Amazon EC2 instance or instances and deploy your Tic\-Tac\-Toe application bundle on them\. After the Elastic Beanstalk environment is ready, you provide the configuration file name by adding the `CONFIG_FILE` environment variable\.

1. Create the DynamoDB table\. Using the Amazon DynamoDB service, you create the Games table on AWS, rather than locally on your computer\. Remember, this table has a simple primary key made of the `GameId` partition key of string type\. 

1. Test the game in production\. 

## 3\.1: Create an IAM Role for Amazon EC2<a name="TicTacToe.DeployInProd.IAMCreateRole"></a>

Creating an IAM role of the **Amazon EC2** type will allow the Amazon EC2 instance that is running your Tic\-Tac\-Toe application to assume the correct IAM role and make application requests to access the Games table\. When creating the role, choose the **Custom Policy** option and copy and paste the following policy\.

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Action":[
            "dynamodb:ListTables"
         ],
         "Effect":"Allow",
         "Resource":"*"
      },
      {
         "Action":[
            "dynamodb:*"
         ],
         "Effect":"Allow",
         "Resource":[
            "arn:aws:dynamodb:us-west-2:922852403271:table/Games",
            "arn:aws:dynamodb:us-west-2:922852403271:table/Games/index/*"
         ]
      }
   ]
}
```

For further instructions, go to [Creating a Role for an AWS Service \(AWS Management Console\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html) in the *IAM User Guide\.*

## 3\.2: Create the Games Table in Amazon DynamoDB<a name="TicTacToe.DeployInProd.CreateTable"></a>

The Games table in DynamoDB stores game data\. If the table does not exist, the application will create the table for you\. In this case, we will let the application create the Games table\.

## 3\.3: Bundle and Deploy Tic\-Tac\-Toe Application Code<a name="TicTacToe.DeployInProd.IAMBundleDeployCode"></a>

If you followed this example's steps, then you already have the downloaded the Tic\-Tac\-Toe application\. If not, download the application and extract all the files to a folder on your local computer\. For instructions, see [Step 1: Deploy and Test Locally](TicTacToe.Phase1.md)\.

After you extract all files, note that you will have a `code` folder\. To hand off this folder to Electric Beanstalk, you will bundle the contents of this folder as a `.zip` file\. First, you need to add a configuration file to that folder\. Your application will use the region and endpoint information to create a DynamoDB table in the specified region and make subsequent table operation requests using the specified endpoint\.

1. Switch to the folder where you downloaded the Tic\-Tac\-Toe application\.

1. In the root folder of the application, create a text file named `beanstalk.config` with the following content:

   ```
   [dynamodb] 
   region=<AWS region> 
   endpoint=<DynamoDB endpoint>
   ```

   For example, you might use the following content:

   ```
   [dynamodb] 
   region=us-west-2 
   endpoint=dynamodb.us-west-2.amazonaws.com
   ```

   For a list of available regions, go to [Amazon DynamoDB](https://docs.aws.amazon.com/general/latest/gr/rande.html#ddb_region) in the *Amazon Web Services General Reference\.* 
**Important**  
The region specified in the configuration file is the location where the Tic\-Tac\-Toe application creates the Games table in DynamoDB\. You must create the Elastic Beanstalk application discussed in the next section in the same region\. 
**Note**  
When you create your Elastic Beanstalk application, you will request to launch an environment where you can choose the environment type\. To test the Tic\-Tac\-Toe example application, you can choose the **Single Instance** environment type, skip the following, and go to the next step\.   
However, note that the **Load balancing, autoscaling** environment type provides a highly available and scalable environment, something you should consider when you create and deploy other applications\. If you choose this environment type, you will also need to generate a UUID and add it to the configuration file as shown following:  

   ```
   [dynamodb] 
   region=us-west-2 
   endpoint=dynamodb.us-west-2.amazonaws.com
   [flask] 
   secret_key= 284e784d-1a25-4a19-92bf-8eeb7a9example
   ```
In client\-server communication when the server sends response, for security's sake the server sends a signed cookie that the client sends back to the server in the next request\. When there is only one server, the server can locally generate an encryption key when it starts\. When there are many servers, they all need to know the same encryption key; otherwise, they won't be able to read cookies set by the peer servers\. By adding `secret_key` to the configuration file, we tell all servers to use this encryption key\.

1. Zip the content of the root folder of the application \(which includes the `beanstalk.config` file\)—for example, `TicTacToe.zip`\. 

1. Upload the `.zip` file to an Amazon Simple Storage Service \(Amazon S3\) bucket\. In the next section, you provide this `.zip` file to Elastic Beanstalk to upload on the server or servers\.

   For instructions on how to upload to an Amazon S3 bucket, go to the [Create a Bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html) and [Add an Object to a Bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/PuttingAnObjectInABucket.html) topics in the *Amazon Simple Storage Service Getting Started Guide\.*

## 3\.4: Set Up the AWS Elastic Beanstalk Environment<a name="TicTacToe.DeployInProd.SetUpElasticBeanstalk"></a>

In this step, you create an Elastic Beanstalk application, which is a collection of components including environments\. For this example, you will launch one Amazon EC2 instance to deploy and run your Tic\-Tac\-Toe application\.

1. Type the following custom URL to set up an Elastic Beanstalk console to set up the environment:

   ```
   https://console.aws.amazon.com/elasticbeanstalk/?region=<AWS-Region>#/newApplication
   ?applicationName=TicTacToeyour-name
   &solutionStackName=Python
   &sourceBundleUrl=https://s3.amazonaws.com/<bucket-name>/TicTacToe.zip
   &environmentType=SingleInstance
   &instanceType=t1.micro
   ```

   For more information about custom URLs, go to [Constructing a Launch Now URL](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/launch-now-url.html) in the *AWS Elastic Beanstalk Developer Guide\. *For the URL, note the following:
   + You will need to provide an AWS region name \(the same as the one you provided in the configuration file\), an Amazon S3 bucket name, and the object name\. 
   + For testing, the URL requests the **SingleInstance** environment type, and **t1\.micro** as the instance type\.
   + The application name must be unique\. Thus, in the preceding URL, we suggest you prepend your name to the `applicationName`\.

   Doing this opens the Elastic Beanstalk console\. In some cases, you might need to sign in\.

1. In the Elastic Beanstalk console, choose **Review and Launch**, and then choose **Launch**\. 

1. Note the URL for future reference\. This URL opens your Tic\-Tac\-Toe application home page\.   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/tic-tac-toe-beanstalk-setup-50.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

1. Configure the Tic\-Tac\-Toe application so it knows the location of the configuration file\.

   After Elastic Beanstalk creates the application, choose **Configuration**\. 

   1. Choose the gear box next to **Software Configuration**, as shown in the following screenshot\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/tic-tac-toe-beanstalk-setup-60.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

   1. At the end of the **Environment Properties** section, type `CONFIG_FILE` and its value `beanstalk.config`, and then choose **Save**\.

      It might take a few minutes for this environment update to complete\.   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/tic-tac-toe-beanstalk-setup-70.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

   After the update completes, you can play the game\.

1. In the browser, type the URL you copied in the previous step, as shown in the following example\. 

   ```
   http://<pen-name>.elasticbeanstalk.com
   ```

   Doing this will open the application home page\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/tic-tac-toe-inprod-playgame-10.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

1. Log in as testuser1, and choose **CREATE** to start a new tic\-tac\-toe game\. 

1. Type **testuser2** in the **Choose an Opponent** box\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/tic-tac-toe-inprod-playgame-20.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

1. Open another browser window\. 

   Make sure that you clear all cookies in your browser window so you won't be logged in as same user\.

1. Type the same URL to open the application home page, as shown in the following example: 

   ```
   http://<env-name>.elasticbeanstalk.com
   ```

1. Log in as testuser2\.

1. For the invitation from testuser1 in the list of pending invitations, choose **accept**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/tic-tac-toe-inprod-playgame-30.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

1. Now the game page will appear\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/tic-tac-toe-inprod-playgame-40.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

   Both testuser1 and testuser2 can play the game\. For each move, the application will save the move in the corresponding item in the Games table\. 