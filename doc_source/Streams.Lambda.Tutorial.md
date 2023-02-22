# Tutorial \#1: Using filters to process all events with Amazon DynamoDB and AWS Lambda using the AWS CLI<a name="Streams.Lambda.Tutorial"></a>

 

**Topics**
+ [Step 1: Create a DynamoDB table with a stream enabled](#Streams.Lambda.Tutorial.CreateTable)
+ [Step 2: Create a Lambda execution role](#Streams.Lambda.Tutorial.CreateRole)
+ [Step 3: Create an Amazon SNS topic](#Streams.Lambda.Tutorial.SNSTopic)
+ [Step 4: Create and test a Lambda function](#Streams.Lambda.Tutorial.LambdaFunction)
+ [Step 5: Create and test a trigger](#Streams.Lambda.Tutorial.CreateTrigger)

In this tutorial, you will create an AWS Lambda trigger to process a stream from a DynamoDB table\.

The scenario for this tutorial is Woofer, a simple social network\. Woofer users communicate using *barks* \(short text messages\) that are sent to other Woofer users\. The following diagram shows the components and workflow for this application\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/StreamsAndTriggers.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

1. A user writes an item to a DynamoDB table \(`BarkTable`\)\. Each item in the table represents a bark\.

1. A new stream record is written to reflect that a new item has been added to `BarkTable`\.

1. The new stream record triggers an AWS Lambda function \(`publishNewBark`\)\.

1. If the stream record indicates that a new item was added to `BarkTable`, the Lambda function reads the data from the stream record and publishes a message to a topic in Amazon Simple Notification Service \(Amazon SNS\)\.

1. The message is received by subscribers to the Amazon SNS topic\. \(In this tutorial, the only subscriber is an email address\.\)

**Before You Begin**  
This tutorial uses the AWS Command Line Interface AWS CLI\. If you have not done so already, follow the instructions in the [AWS Command Line Interface User Guide](https://docs.aws.amazon.com/cli/latest/userguide/) to install and configure the AWS CLI\.

## Step 1: Create a DynamoDB table with a stream enabled<a name="Streams.Lambda.Tutorial.CreateTable"></a>

In this step, you create a DynamoDB table \(`BarkTable`\) to store all of the barks from Woofer users\. The primary key is composed of `Username` \(partition key\) and `Timestamp` \(sort key\)\. Both of these attributes are of type string\.

`BarkTable` has a stream enabled\. Later in this tutorial, you create a trigger by associating an AWS Lambda function with the stream\.

1. Enter the following command to create the table\.

   ```
   aws dynamodb create-table \
       --table-name BarkTable \
       --attribute-definitions AttributeName=Username,AttributeType=S AttributeName=Timestamp,AttributeType=S \
       --key-schema AttributeName=Username,KeyType=HASH  AttributeName=Timestamp,KeyType=RANGE \
       --provisioned-throughput ReadCapacityUnits=5,WriteCapacityUnits=5 \
       --stream-specification StreamEnabled=true,StreamViewType=NEW_AND_OLD_IMAGES
   ```

1. In the output, look for the `LatestStreamArn`\.

   ```
   ...
   "LatestStreamArn": "arn:aws:dynamodb:region:accountID:table/BarkTable/stream/timestamp
   ...
   ```

   Make a note of the `region` and the `accountID`, because you need them for the other steps in this tutorial\.

## Step 2: Create a Lambda execution role<a name="Streams.Lambda.Tutorial.CreateRole"></a>

In this step, you create an AWS Identity and Access Management \(IAM\) role \(`WooferLambdaRole`\) and assign permissions to it\. This role is used by the Lambda function that you create in [Step 4: Create and test a Lambda function](#Streams.Lambda.Tutorial.LambdaFunction)\. 

You also create a policy for the role\. The policy contains all of the permissions that the Lambda function needs at runtime\.

1. Create a file named `trust-relationship.json` with the following contents\.

   ```
   {
      "Version": "2012-10-17",
      "Statement": [
        {
          "Effect": "Allow",
          "Principal": {
            "Service": "lambda.amazonaws.com"
          },
          "Action": "sts:AssumeRole"
        }
      ]
    }
   ```

1. Enter the following command to create `WooferLambdaRole`\.

   ```
   aws iam create-role --role-name WooferLambdaRole \
       --path "/service-role/" \
       --assume-role-policy-document file://trust-relationship.json
   ```

1. Create a file named `role-policy.json` with the following contents\. \(Replace `region` and `accountID` with your AWS Region and account ID\.\)

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": "lambda:InvokeFunction",
               "Resource": "arn:aws:lambda:region:accountID:function:publishNewBark*"
           },
           {
               "Effect": "Allow",
               "Action": [
                   "logs:CreateLogGroup",
                   "logs:CreateLogStream",
                   "logs:PutLogEvents"
               ],
               "Resource": "arn:aws:logs:region:accountID:*"
           },
           {
               "Effect": "Allow",
               "Action": [
                   "dynamodb:DescribeStream",
                   "dynamodb:GetRecords",
                   "dynamodb:GetShardIterator",
                   "dynamodb:ListStreams"
               ],
               "Resource": "arn:aws:dynamodb:region:accountID:table/BarkTable/stream/*"
           },
           {
               "Effect": "Allow",
               "Action": [
                   "sns:Publish"
               ],
               "Resource": [
                   "*"
               ]
           }
       ]
   }
   ```

   The policy has four statements that allow `WooferLambdaRole` to do the following:
   + Run a Lambda function \(`publishNewBark`\)\. You create the function later in this tutorial\.
   + Access Amazon CloudWatch Logs\. The Lambda function writes diagnostics to CloudWatch Logs at runtime\.
   + Read data from the DynamoDB stream for `BarkTable`\.
   + Publish messages to Amazon SNS\.

1. Enter the following command to attach the policy to `WooferLambdaRole`\.

   ```
   aws iam put-role-policy --role-name WooferLambdaRole \
       --policy-name WooferLambdaRolePolicy \
       --policy-document file://role-policy.json
   ```

## Step 3: Create an Amazon SNS topic<a name="Streams.Lambda.Tutorial.SNSTopic"></a>

In this step, you create an Amazon SNS topic \(`wooferTopic`\) and subscribe an email address to it\. Your Lambda function uses this topic to publish new barks from Woofer users\.

1. Enter the following command to create a new Amazon SNS topic\.

   ```
   aws sns create-topic --name wooferTopic
   ```

1. Enter the following command to subscribe an email address to `wooferTopic`\. \(Replace `region` and `accountID` with your AWS Region and account ID, and replace `example@example.com` with a valid email address\.\)

   ```
   aws sns subscribe \
       --topic-arn arn:aws:sns:region:accountID:wooferTopic \
       --protocol email \
       --notification-endpoint example@example.com
   ```

1. Amazon SNS sends a confirmation message to your email address\. Choose the **Confirm subscription** link in that message to complete the subscription process\.

## Step 4: Create and test a Lambda function<a name="Streams.Lambda.Tutorial.LambdaFunction"></a>

In this step, you create an AWS Lambda function \(`publishNewBark`\) to process stream records from `BarkTable`\.

The `publishNewBark` function processes only the stream events that correspond to new items in `BarkTable`\. The function reads data from such an event, and then invokes Amazon SNS to publish it\.

1. Create a file named `publishNewBark.js` with the following contents\. Replace `region` and `accountID` with your AWS Region and account ID\.

   ```
   'use strict';
   var AWS = require("aws-sdk");
   var sns = new AWS.SNS();
   
   exports.handler = (event, context, callback) => {
   
       event.Records.forEach((record) => {
           console.log('Stream record: ', JSON.stringify(record, null, 2));
   
           if (record.eventName == 'INSERT') {
               var who = JSON.stringify(record.dynamodb.NewImage.Username.S);
               var when = JSON.stringify(record.dynamodb.NewImage.Timestamp.S);
               var what = JSON.stringify(record.dynamodb.NewImage.Message.S);
               var params = {
                   Subject: 'A new bark from ' + who,
                   Message: 'Woofer user ' + who + ' barked the following at ' + when + ':\n\n ' + what,
                   TopicArn: 'arn:aws:sns:region:accountID:wooferTopic'
               };
               sns.publish(params, function(err, data) {
                   if (err) {
                       console.error("Unable to send message. Error JSON:", JSON.stringify(err, null, 2));
                   } else {
                       console.log("Results from sending message: ", JSON.stringify(data, null, 2));
                   }
               });
           }
       });
       callback(null, `Successfully processed ${event.Records.length} records.`);
   };
   ```

1. Create a zip file to contain `publishNewBark.js`\. If you have the zip command\-line utility, you can enter the following command to do this\.

   ```
   zip publishNewBark.zip publishNewBark.js
   ```

1. When you create the Lambda function, you specify the Amazon Resource Name \(ARN\) for `WooferLambdaRole`, which you created in [Step 2: Create a Lambda execution role](#Streams.Lambda.Tutorial.CreateRole)\. Enter the following command to retrieve this ARN\.

   ```
   aws iam get-role --role-name WooferLambdaRole
   ```

   In the output, look for the ARN for `WooferLambdaRole`\.

   ```
   ...
   "Arn": "arn:aws:iam::region:role/service-role/WooferLambdaRole"
   ...
   ```

   Enter the following command to create the Lambda function\. Replace *roleARN* with the ARN for `WooferLambdaRole`\.

   ```
   aws lambda create-function \
       --region region \
       --function-name publishNewBark \
       --zip-file fileb://publishNewBark.zip \
       --role roleARN \
       --handler publishNewBark.handler \
       --timeout 5 \
       --runtime nodejs10.x
   ```

1. Now test `publishNewBark` to verify that it works\. To do this, you provide input that resembles a real record from DynamoDB Streams\.

   Create a file named `payload.json` with the following contents\.

   ```
   {
       "Records": [
           {
               "eventID": "7de3041dd709b024af6f29e4fa13d34c",
               "eventName": "INSERT",
               "eventVersion": "1.1",
               "eventSource": "aws:dynamodb",
               "awsRegion": "region",
               "dynamodb": {
                   "ApproximateCreationDateTime": 1479499740,
                   "Keys": {
                       "Timestamp": {
                           "S": "2016-11-18:12:09:36"
                       },
                       "Username": {
                           "S": "John Doe"
                       }
                   },
                   "NewImage": {
                       "Timestamp": {
                           "S": "2016-11-18:12:09:36"
                       },
                       "Message": {
                           "S": "This is a bark from the Woofer social network"
                       },
                       "Username": {
                           "S": "John Doe"
                       }
                   },
                   "SequenceNumber": "13021600000000001596893679",
                   "SizeBytes": 112,
                   "StreamViewType": "NEW_IMAGE"
               },
               "eventSourceARN": "arn:aws:dynamodb:region:123456789012:table/BarkTable/stream/2016-11-16T20:42:48.104"
           }
       ]
   }
   ```

   Enter the following command to test the `publishNewBark` function\.

   ```
   aws lambda invoke --function-name publishNewBark --payload file://payload.json --cli-binary-format raw-in-base64-out output.txt
   ```

   If the test was successful, you will see the following output\.

   ```
   {
       "StatusCode": 200
   }
   ```

   In addition, the `output.txt` file will contain the following text\.

   ```
   "Successfully processed 1 records."
   ```

   You will also receive a new email message within a few minutes\.
**Note**  
AWS Lambda writes diagnostic information to Amazon CloudWatch Logs\. If you encounter errors with your Lambda function, you can use these diagnostics for troubleshooting purposes:  
Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.
In the navigation pane, choose **Logs**\.
Choose the following log group: `/aws/lambda/publishNewBark`
Choose the latest log stream to view the output \(and errors\) from the function\.

## Step 5: Create and test a trigger<a name="Streams.Lambda.Tutorial.CreateTrigger"></a>

In [Step 4: Create and test a Lambda function](#Streams.Lambda.Tutorial.LambdaFunction), you tested the Lambda function to ensure that it ran correctly\. In this step, you create a *trigger* by associating the Lambda function \(`publishNewBark`\) with an event source \(the `BarkTable` stream\)\.

1. When you create the trigger, you need to specify the ARN for the `BarkTable` stream\. Enter the following command to retrieve this ARN\.

   ```
   aws dynamodb describe-table --table-name BarkTable
   ```

   In the output, look for the `LatestStreamArn`\.

   ```
   ...
    "LatestStreamArn": "arn:aws:dynamodb:region:accountID:table/BarkTable/stream/timestamp
   ...
   ```

1. Enter the following command to create the trigger\. Replace `streamARN` with the actual stream ARN\.

   ```
   aws lambda create-event-source-mapping \
       --region region \
       --function-name publishNewBark \
       --event-source streamARN  \
       --batch-size 1 \
       --starting-position TRIM_HORIZON
   ```

1. Test the trigger\. Enter the following command to add an item to `BarkTable`\.

   ```
   aws dynamodb put-item \
       --table-name BarkTable \
       --item Username={S="Jane Doe"},Timestamp={S="2016-11-18:14:32:17"},Message={S="Testing...1...2...3"}
   ```

   You should receive a new email message within a few minutes\.

1. Open the DynamoDB console and add a few more items to `BarkTable`\. You must specify values for the `Username` and `Timestamp` attributes\. \(You should also specify a value for `Message`, even though it is not required\.\) You should receive a new email message for each item you add to `BarkTable`\.

   The Lambda function processes only new items that you add to `BarkTable`\. If you update or delete an item in the table, the function does nothing\.

**Note**  
AWS Lambda writes diagnostic information to Amazon CloudWatch Logs\. If you encounter errors with your Lambda function, you can use these diagnostics for troubleshooting purposes\.  
Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.
In the navigation pane, choose **Logs**\.
Choose the following log group: `/aws/lambda/publishNewBark`
Choose the latest log stream to view the output \(and errors\) from the function\.