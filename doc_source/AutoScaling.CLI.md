# Using the AWS CLI to manage DynamoDB auto scaling<a name="AutoScaling.CLI"></a>

Instead of using the AWS Management Console, you can use the AWS Command Line Interface \(AWS CLI\) to manage Amazon DynamoDB auto scaling\. The tutorial in this section demonstrates how to install and configure the AWS CLI for managing DynamoDB auto scaling\. In this tutorial, you do the following:
+ Create a DynamoDB table named `TestTable`\. The initial throughput settings are 5 read capacity units and 5 write capacity units\.
+ Create an Application Auto Scaling policy for `TestTable`\. The policy seeks to maintain a 50 percent target ratio between consumed write capacity and provisioned write capacity\. The range for this metric is between 5 and 10 write capacity units\. \(Application Auto Scaling is not allowed to adjust the throughput beyond this range\.\)
+ Run a Python program to drive write traffic to `TestTable`\. When the target ratio exceeds 50 percent for a sustained period of time, Application Auto Scaling notifies DynamoDB to adjust the throughput of `TestTable` upward to maintain the 50 percent target utilization\.
+ Verify that DynamoDB has successfully adjusted the provisioned write capacity for `TestTable`\.

**Topics**
+ [Before you begin](#AutoScaling.CLI.BeforeYouBegin)
+ [Step 1: Create a DynamoDB table](#AutoScaling.CLI.CreateTable)
+ [Step 2: Register a scalable target](#AutoScaling.CLI.RegisterScalableTarget)
+ [Step 3: Create a scaling policy](#AutoScaling.CLI.CreateScalingPolicy)
+ [Step 4: Drive write traffic to TestTable](#AutoScaling.CLI.DriveTraffic)
+ [Step 5: View Application Auto Scaling actions](#AutoScaling.CLI.ViewCWAlarms)
+ [\(Optional\) Step 6: Clean up](#AutoScaling.CLI.CleanUp)

## Before you begin<a name="AutoScaling.CLI.BeforeYouBegin"></a>

Complete the following tasks before starting the tutorial\.

### Install the AWS CLI<a name="AutoScaling.CLI.BeforeYouBegin.InstallCLI"></a>

If you haven't already done so, you must install and configure the AWS CLI\. To do this, follow these instructions in the *AWS Command Line Interface User Guide*:
+ [Installing the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/installing.html)
+ [Configuring the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html)

### Install Python<a name="AutoScaling.CLI.BeforeYouBegin.InstallPython"></a>

Part of this tutorial requires you to run a Python program \(see [Step 4: Drive write traffic to TestTable](#AutoScaling.CLI.DriveTraffic)\)\. If you don't already have it installed, you can [download Python](https://www.python.org/downloads)\. 

## Step 1: Create a DynamoDB table<a name="AutoScaling.CLI.CreateTable"></a>

In this step, you use the AWS CLI to create `TestTable`\. The primary key consists of `pk` \(partition key\) and `sk` \(sort key\)\. Both of these attributes are of type `Number`\. The initial throughput settings are 5 read capacity units and 5 write capacity units\.

1. Use the following AWS CLI command to create the table\.

   ```
   aws dynamodb create-table \
       --table-name TestTable \
       --attribute-definitions \
           AttributeName=pk,AttributeType=N \
           AttributeName=sk,AttributeType=N \
       --key-schema \
           AttributeName=pk,KeyType=HASH \
           AttributeName=sk,KeyType=RANGE \
       --provisioned-throughput ReadCapacityUnits=5,WriteCapacityUnits=5
   ```

1. To check the status of the table, use the following command\.

   ```
   aws dynamodb describe-table \
       --table-name TestTable \
       --query "Table.[TableName,TableStatus,ProvisionedThroughput]"
   ```

   The table is ready for use when its status is `ACTIVE`\.

## Step 2: Register a scalable target<a name="AutoScaling.CLI.RegisterScalableTarget"></a>

Next you register the table's write capacity as a scalable target with Application Auto Scaling\. This allows Application Auto Scaling to adjust the provisioned write capacity for *TestTable*, but only within the range of 5–10 capacity units\.

**Note**  
DynamoDB auto scaling requires the presence of a service linked role \(`AWSServiceRoleForApplicationAutoScaling_DynamoDBTable`\) that performs auto scaling actions on your behalf\. This role is created automatically for you\. For more information, see [Service\-linked roles for Application Auto Scaling](https://docs.aws.amazon.com/autoscaling/application/userguide/application-auto-scaling-service-linked-roles.html) in the *Application Auto Scaling User Guide*\. 

1. Enter the following command to register the scalable target\. 

   ```
   aws application-autoscaling register-scalable-target \
       --service-namespace dynamodb \
       --resource-id "table/TestTable" \
       --scalable-dimension "dynamodb:table:WriteCapacityUnits" \
       --min-capacity 5 \
       --max-capacity 10
   ```

1. To verify the registration, use the following command\.

   ```
   aws application-autoscaling describe-scalable-targets \
       --service-namespace dynamodb \
       --resource-id "table/TestTable"
   ```
**Note**  
 You can also register a scalable target against a global secondary index\. For example, for a global secondary index \("test\-index"\), the resource ID and scalable dimension arguments are updated appropriately\.   

   ```
   aws application-autoscaling register-scalable-target \
       --service-namespace dynamodb \
       --resource-id "table/TestTable/index/test-index" \
       --scalable-dimension "dynamodb:index:WriteCapacityUnits" \
       --min-capacity 5 \
       --max-capacity 10
   ```

## Step 3: Create a scaling policy<a name="AutoScaling.CLI.CreateScalingPolicy"></a>

In this step, you create a scaling policy for `TestTable`\. The policy defines the details under which Application Auto Scaling can adjust your table's provisioned throughput, and what actions to take when it does so\. You associate this policy with the scalable target that you defined in the previous step \(write capacity units for the `TestTable` table\)\.

The policy contains the following elements:
+ `PredefinedMetricSpecification`—The metric that Application Auto Scaling is allowed to adjust\. For DynamoDB, the following values are valid values for `PredefinedMetricType`:
  + `DynamoDBReadCapacityUtilization`
  + `DynamoDBWriteCapacityUtilization`
+ `ScaleOutCooldown`—The minimum amount of time \(in seconds\) between each Application Auto Scaling event that increases provisioned throughput\. This parameter allows Application Auto Scaling to continuously, but not aggressively, increase the throughput in response to real\-world workloads\. The default setting for `ScaleOutCooldown` is 0\.
+ `ScaleInCooldown`—The minimum amount of time \(in seconds\) between each Application Auto Scaling event that decreases provisioned throughput\. This parameter allows Application Auto Scaling to decrease the throughput gradually and predictably\. The default setting for `ScaleInCooldown` is 0\.
+ `TargetValue`—Application Auto Scaling ensures that the ratio of consumed capacity to provisioned capacity stays at or near this value\. You define `TargetValue` as a percentage\.

**Note**  
To further understand how `TargetValue` works, suppose that you have a table with a provisioned throughput setting of 200 write capacity units\. You decide to create a scaling policy for this table, with a `TargetValue` of 70 percent\.  
Now suppose that you begin driving write traffic to the table so that the actual write throughput is 150 capacity units\. The consumed\-to\-provisioned ratio is now \(150 / 200\), or 75 percent\. This ratio exceeds your target, so Application Auto Scaling increases the provisioned write capacity to 215 so that the ratio is \(150 / 215\), or 69\.77 percent—as close to your `TargetValue` as possible, but not exceeding it\.

For `TestTable`, you set `TargetValue` to 50 percent\. Application Auto Scaling adjusts the table's provisioned throughput within the range of 5–10 capacity units \(see [Step 2: Register a scalable target](#AutoScaling.CLI.RegisterScalableTarget)\) so that the consumed\-to\-provisioned ratio remains at or near 50 percent\. You set the values for `ScaleOutCooldown` and `ScaleInCooldown` to 60 seconds\.

1. Create a file named `scaling-policy.json` with the following contents\.

   ```
   {
       "PredefinedMetricSpecification": {
           "PredefinedMetricType": "DynamoDBWriteCapacityUtilization"
       },
       "ScaleOutCooldown": 60,
       "ScaleInCooldown": 60,
       "TargetValue": 50.0
   }
   ```

1. Use the following AWS CLI command to create the policy\.

   ```
   aws application-autoscaling put-scaling-policy \
       --service-namespace dynamodb \
       --resource-id "table/TestTable" \
       --scalable-dimension "dynamodb:table:WriteCapacityUnits" \
       --policy-name "MyScalingPolicy" \
       --policy-type "TargetTrackingScaling" \
       --target-tracking-scaling-policy-configuration file://scaling-policy.json
   ```

1. In the output, note that Application Auto Scaling has created two Amazon CloudWatch alarms—one each for the upper and lower boundary of the scaling target range\.

1. Use the following AWS CLI command to view more details about the scaling policy\.

   ```
   aws application-autoscaling describe-scaling-policies \
       --service-namespace dynamodb \
       --resource-id "table/TestTable" \
       --policy-name "MyScalingPolicy"
   ```

1. In the output, verify that the policy settings match your specifications from [Step 2: Register a scalable target](#AutoScaling.CLI.RegisterScalableTarget) and [Step 3: Create a scaling policy](#AutoScaling.CLI.CreateScalingPolicy)\.

## Step 4: Drive write traffic to TestTable<a name="AutoScaling.CLI.DriveTraffic"></a>

Now you can test your scaling policy by writing data to `TestTable`\. To do this, you run a Python program\.

1. Create a file named `bulk-load-test-table.py` with the following contents\.

   ```
   import boto3
   dynamodb = boto3.resource('dynamodb')
   
   table = dynamodb.Table("TestTable")
   
   filler = "x" * 100000
   
   i = 0
   while (i < 10):
       j = 0
       while (j < 10):
           print (i, j)
           
           table.put_item(
               Item={
                   'pk':i,
                   'sk':j,
                   'filler':{"S":filler}
               }
           )
           j += 1
       i += 1
   ```

1. Enter the following command to run the program\.

   `python bulk-load-test-table.py`

   The provisioned write capacity for `TestTable` is very low \(5 write capacity units\), so the program stalls occasionally due to write throttling\. This is expected behavior\.

   Let the program continue running while you move on to the next step\.

## Step 5: View Application Auto Scaling actions<a name="AutoScaling.CLI.ViewCWAlarms"></a>

 In this step, you view the Application Auto Scaling actions that are initiated on your behalf\. You also verify that Application Auto Scaling has updated the provisioned write capacity for `TestTable`\.

1. Enter the following command to view the Application Auto Scaling actions\.

   ```
   aws application-autoscaling describe-scaling-activities \
       --service-namespace dynamodb
   ```

   Rerun this command occasionally, while the Python program is running\. \(It takes several minutes before your scaling policy is invoked\.\) You should eventually see the following output\.

   ```
   ...
   {
       "ScalableDimension": "dynamodb:table:WriteCapacityUnits", 
       "Description": "Setting write capacity units to 10.", 
       "ResourceId": "table/TestTable", 
       "ActivityId": "0cc6fb03-2a7c-4b51-b67f-217224c6b656", 
       "StartTime": 1489088210.175, 
       "ServiceNamespace": "dynamodb", 
       "EndTime": 1489088246.85, 
       "Cause": "monitor alarm AutoScaling-table/TestTable-AlarmHigh-1bb3c8db-1b97-4353-baf1-4def76f4e1b9 in state ALARM triggered policy MyScalingPolicy", 
       "StatusMessage": "Successfully set write capacity units to 10. Change successfully fulfilled by dynamodb.", 
       "StatusCode": "Successful"
   }, 
   ...
   ```

   This indicates that Application Auto Scaling has issued an `UpdateTable` request to DynamoDB\.

1. Enter the following command to verify that DynamoDB increased the table's write capacity\.

   ```
   aws dynamodb describe-table \
       --table-name TestTable \
       --query "Table.[TableName,TableStatus,ProvisionedThroughput]"
   ```

   The `WriteCapacityUnits` should have been scaled from `5` to `10`\.

## \(Optional\) Step 6: Clean up<a name="AutoScaling.CLI.CleanUp"></a>

In this tutorial, you created several resources\. You can delete these resources if you no longer need them\.

1. Delete the scaling policy for `TestTable`\.

   ```
   aws application-autoscaling delete-scaling-policy \
       --service-namespace dynamodb \
       --resource-id "table/TestTable" \
       --scalable-dimension "dynamodb:table:WriteCapacityUnits" \
       --policy-name "MyScalingPolicy"
   ```

1. Deregister the scalable target\.

   ```
   aws application-autoscaling deregister-scalable-target \
       --service-namespace dynamodb \
       --resource-id "table/TestTable" \
       --scalable-dimension "dynamodb:table:WriteCapacityUnits"
   ```

1. Delete the `TestTable` table\.

   ```
   aws dynamodb delete-table --table-name TestTable
   ```