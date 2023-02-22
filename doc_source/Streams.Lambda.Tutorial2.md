# Tutorial \#2: Using filters to process some events with DynamoDB and Lambda\.<a name="Streams.Lambda.Tutorial2"></a>

**Topics**
+ [Putting it all together \- AWS CloudFormation](#Streams.Lambda.Tutorial2.Cloudformation)
+ [Putting it all together \- CDK](#Streams.Lambda.Tutorial2.CDK)

 

In this tutorial, you will create an AWS Lambda trigger to process only some events in a stream from a DynamoDB table\.

With [Lambda event filtering](https://docs.aws.amazon.com/lambda/latest/dg/invocation-eventfiltering.html) you can use filter expressions to control which events Lambda sends to your function for processing\. You can configure up to 5 different filters per DynamoDB streams\. If you are using batching windows, Lambda applies the filter criteria to each new event to see if it should be included in the current batch\.

Filters are applied via structures called `FilterCriteria`\. The 3 main attributes of `FilterCriteria` are `metadata properties`, `data properties` and `filter patterns`\. 

Here is an example structure of a DynamoDB Streams event:

```
{
  "eventID": "c9fbe7d0261a5163fcb6940593e41797",
  "eventName": "INSERT",
  "eventVersion": "1.1",
  "eventSource": "aws:dynamodb",
  "awsRegion": "us-east-2",
  "dynamodb": {
    "ApproximateCreationDateTime": 1664559083.0,
    "Keys": {
      "SK": { "S": "PRODUCT#CHOCOLATE#DARK#1000" },
      "PK": { "S": "COMPANY#1000" }
    },
    "NewImage": {
      "quantity": { "N": "50" },
      "company_id": { "S": "1000" },
      "fabric": { "S": "Florida Chocolates" },
      "price": { "N": "15" },
      "stores": { "N": "5" },
      "product_id": { "S": "1000" },
      "SK": { "S": "PRODUCT#CHOCOLATE#DARK#1000" },
      "PK": { "S": "COMPANY#1000" },
      "state": { "S": "FL" },
      "type": { "S": "" }
    },
    "SequenceNumber": "700000000000888747038",
    "SizeBytes": 174,
    "StreamViewType": "NEW_AND_OLD_IMAGES"
  },
  "eventSourceARN": "arn:aws:dynamodb:us-east-2:111122223333:table/chocolate-table-StreamsSampleDDBTable-LUOI6UXQY7J1/stream/2022-09-30T17:05:53.209"
}
```

The `metadata properties` are the fields of the event object\. In the case of DynamoDB Streams, the `metadata properties` are fields like `dynamodb` or `eventName`\. 

The `data properties` are the fields of the event body\. To filter on `data properties`, make sure to contain them in `FilterCriteria` within the proper key\. For DynamoDB event sources, the data key is `NewImage` or `OldImage`\.

Finally, the filter rules will define the filter expression that you want to apply to a specific property\. Here are some examples:


| Comparison operator | Example | Rule syntax \(Partial\) | 
| --- | --- | --- | 
|  Null  |  Product Type is null  |  `{ "product_type": { "S": null } } `  | 
|  Empty  |  Product name is empty  |  `{ "product_name": { "S": [ ""] } } `  | 
|  Equals  |  State equals Florida  |  `{ "state": { "S": ["FL"] } } `  | 
|  And  |  Product state equals Florida and product category Chocolate  |  `{ "state": { "S": ["FL"] } , "category": { "S": [ "CHOCOLATE"] } } `  | 
|  Or  |  Product state is Florida or California  |  `{ "state": { "S": ["FL","CA"] } } `  | 
|  Not  |  Product state is not Florida  |  `{"state": {"S": [{"anything-but": ["FL"]}]}}`  | 
|  Exists  |  Product Homemade exists  |  `{"homemade": [{"exists": True}]}`  | 
|  Does not exist  |  Product Homemade does not exists  |  `{"homemade": [{"exists": False}]}`  | 
|  Begins with  |  PK begins with COMPANY  |  `{"PK": {"S": [{"prefix": "COMPANY"}]}}`  | 

You can specify up to 5 event filtering patterns for a Lambda function\. Notice that each one of those 5 events will be evaluated as a logical OR\. So if you configure two filters named `Filter_One` and `Filter_Two`, the Lambda function will execute `Filter_One` OR `Filter_Two`\.

**Note**  
In the [Lambda event filtering](lambda/latest/dg/invocation-eventfiltering.html) page there are some options to filter and compare numeric values, however in the case of DynamoDB filter events it doesn’t apply because numbers in DynamoDB are stored as strings\. For example ` "quantity": { "N": "50" }`, we know its a number because of the `"N"` property\.

## Putting it all together \- AWS CloudFormation<a name="Streams.Lambda.Tutorial2.Cloudformation"></a>

To show event filtering functionality in practice, here is a sample CloudFormation template\. This template will generate a Simple DynamoDB table with a Partition Key PK and a Sort Key SK with Amazon DynamoDB Streams enabled\. It will create a lambda function and a simple Lambda Execution role that will allow write logs to Amazon Cloudwatch, and read the events from the Amazon DynamoDB Stream\. It will also add the event source mapping between the DynamoDB Streams and the Lambda function, so the function can be executed every time there is an event in the Amazon DynamoDB Stream\.

```
AWSTemplateFormatVersion: "2010-09-09"

Description: Sample application that presents AWS Lambda event source filtering 
with Amazon DynamoDB Streams.

Resources:
  StreamsSampleDDBTable:
    Type: AWS::DynamoDB::Table
    Properties:
      AttributeDefinitions:
        - AttributeName: "PK"
          AttributeType: "S"
        - AttributeName: "SK"
          AttributeType: "S"
      KeySchema:
        - AttributeName: "PK"
          KeyType: "HASH"
        - AttributeName: "SK"
          KeyType: "RANGE"
      StreamSpecification:
        StreamViewType: "NEW_AND_OLD_IMAGES"
      ProvisionedThroughput:
        ReadCapacityUnits: 5
        WriteCapacityUnits: 5

  LambdaExecutionRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: "2012-10-17"
        Statement:
          - Effect: Allow
            Principal:
              Service:
                - lambda.amazonaws.com
            Action:
              - sts:AssumeRole
      Path: "/"
      Policies:
        - PolicyName: root
          PolicyDocument:
            Version: "2012-10-17"
            Statement:
              - Effect: Allow
                Action:
                  - logs:CreateLogGroup
                  - logs:CreateLogStream
                  - logs:PutLogEvents
                Resource: arn:aws:logs:*:*:*
              - Effect: Allow
                Action:
                  - dynamodb:DescribeStream
                  - dynamodb:GetRecords
                  - dynamodb:GetShardIterator
                  - dynamodb:ListStreams
                Resource: !GetAtt StreamsSampleDDBTable.StreamArn

  EventSourceDDBTableStream:
    Type: AWS::Lambda::EventSourceMapping
    Properties:
      BatchSize: 1
      Enabled: True
      EventSourceArn: !GetAtt StreamsSampleDDBTable.StreamArn
      FunctionName: !GetAtt ProcessEventLambda.Arn
      StartingPosition: LATEST

  ProcessEventLambda:
    Type: AWS::Lambda::Function
    Properties:
      Runtime: python3.7
      Timeout: 300
      Handler: index.handler
      Role: !GetAtt LambdaExecutionRole.Arn
      Code:
        ZipFile: |
          import logging

          LOGGER = logging.getLogger()
          LOGGER.setLevel(logging.INFO)

          def handler(event, context):
            LOGGER.info('Received Event: %s', event)
            for rec in event['Records']:
              LOGGER.info('Record: %s', rec)

Outputs:
  StreamsSampleDDBTable:
    Description: DynamoDB Table ARN created for this example
    Value: !GetAtt StreamsSampleDDBTable.Arn
  StreamARN:
    Description: DynamoDB Table ARN created for this example
    Value: !GetAtt StreamsSampleDDBTable.StreamArn
```

After you deploy this cloud formation template you can insert the following Amazon DynamoDB Item:

```
{
 "PK": "COMPANY#1000",
 "SK": "PRODUCT#CHOCOLATE#DARK",
 "company_id": "1000",
 "type": "",
 "state": "FL",
 "stores": 5,
 "price": 15,
 "quantity": 50,
 "fabric": "Florida Chocolates"
}
```

Thanks to the simple lambda function included inline in this cloud formation template, you will see the events in the Amazon CloudWatch log groups for the lambda function as follows:

```
{
  "eventID": "c9fbe7d0261a5163fcb6940593e41797",
  "eventName": "INSERT",
  "eventVersion": "1.1",
  "eventSource": "aws:dynamodb",
  "awsRegion": "us-east-2",
  "dynamodb": {
    "ApproximateCreationDateTime": 1664559083.0,
    "Keys": {
      "SK": { "S": "PRODUCT#CHOCOLATE#DARK#1000" },
      "PK": { "S": "COMPANY#1000" }
    },
    "NewImage": {
      "quantity": { "N": "50" },
      "company_id": { "S": "1000" },
      "fabric": { "S": "Florida Chocolates" },
      "price": { "N": "15" },
      "stores": { "N": "5" },
      "product_id": { "S": "1000" },
      "SK": { "S": "PRODUCT#CHOCOLATE#DARK#1000" },
      "PK": { "S": "COMPANY#1000" },
      "state": { "S": "FL" },
      "type": { "S": "" }
    },
    "SequenceNumber": "700000000000888747038",
    "SizeBytes": 174,
    "StreamViewType": "NEW_AND_OLD_IMAGES"
  },
  "eventSourceARN": "arn:aws:dynamodb:us-east-2:111122223333:table/chocolate-table-StreamsSampleDDBTable-LUOI6UXQY7J1/stream/2022-09-30T17:05:53.209"
}
```

**Filter Examples**
+ **Only products that matches a given state**

This example modifies the CloudFormation template to include a filter to match all products which come from Florida, with the abbreviation “FL”\.

```
EventSourceDDBTableStream:
    Type: AWS::Lambda::EventSourceMapping
    Properties:
      BatchSize: 1
      Enabled: True
      FilterCriteria:
        Filters:
          - Pattern: '{ "dynamodb": { "NewImage": { "state": { "S": ["FL"] } } } }'
      EventSourceArn: !GetAtt StreamsSampleDDBTable.StreamArn
      FunctionName: !GetAtt ProcessEventLambda.Arn
      StartingPosition: LATEST
```

Once you redeploy the stack, you can add the following DynamoDB item to the table\. Note that it will not appear in the Lambda function logs, because the product in this example is from California\.

```
{
 "PK": "COMPANY#1000",
 "SK": "PRODUCT#CHOCOLATE#DARK#1000",
 "company_id": "1000",
 "fabric": "Florida Chocolates",
 "price": 15,
 "product_id": "1000",
 "quantity": 50,
 "state": "CA",
 "stores": 5,
 "type": ""
}
```
+ **Only the items that starts with some values in the PK and SK**

This example modifies the CloudFormation template to include the following condition:

```
EventSourceDDBTableStream:
    Type: AWS::Lambda::EventSourceMapping
    Properties:
      BatchSize: 1
      Enabled: True
      FilterCriteria:
        Filters:
          - Pattern: '{"dynamodb": {"Keys": {"PK": { "S": [{ "prefix": "COMPANY" }] },"SK": { "S": [{ "prefix": "PRODUCT" }] }}}}'
      EventSourceArn: !GetAtt StreamsSampleDDBTable.StreamArn
      FunctionName: !GetAtt ProcessEventLambda.Arn
      StartingPosition: LATEST
```

Notice the AND condition requires the condition to be inside the pattern, where Keys PK and SK are in the same expression separated by comma\.

Either start with some values on PK and SK or is from certain state\.

This example modifies the CloudFormation template to include the following conditions:

```
  EventSourceDDBTableStream:
    Type: AWS::Lambda::EventSourceMapping
    Properties:
      BatchSize: 1
      Enabled: True
      FilterCriteria:
        Filters:
          - Pattern: '{"dynamodb": {"Keys": {"PK": { "S": [{ "prefix": "COMPANY" }] },"SK": { "S": [{ "prefix": "PRODUCT" }] }}}}'
          - Pattern: '{ "dynamodb": { "NewImage": { "state": { "S": ["FL"] } } } }'
      EventSourceArn: !GetAtt StreamsSampleDDBTable.StreamArn
      FunctionName: !GetAtt ProcessEventLambda.Arn
      StartingPosition: LATEST
```

Notice the OR condition is added by introducing new patterns in the filter section\.

## Putting it all together \- CDK<a name="Streams.Lambda.Tutorial2.CDK"></a>

The following sample CDK project formation template walks through event filtering functionality\. Before working with this CDK project you will need to [ install the pre\-requisites](https://docs.aws.amazon.com/cdk/v2/guide/work-with.html) including [ running preparation scripts](https://docs.aws.amazon.com/cdk/v2/guide/work-with-cdk-python.html)\.

**Create a CDK project**

First create a new AWS CDK project, by invoking `cdk init` in an empty directory\.

```
mkdir ddb_filters
cd ddb_filters
cdk init app --language python
```

The `cdk init` command uses the name of the project folder to name various elements of the project, including classes, subfolders, and files\. Any hyphens in the folder name are converted to underscores\. The name should otherwise follow the form of a Python identifier\. For example, it should not start with a number or contain spaces\.

To work with the new project, activate its virtual environment\. This allows the project's dependencies to be installed locally in the project folder, instead of globally\.

```
source .venv/bin/activate
python -m pip install -r requirements.txt
```

**Note**  
You may recognize this as the Mac/Linux command to activate a virtual environment\. The Python templates include a batch file, `source.bat`, that allows the same command to be used on Windows\. The traditional Windows command `.venv\Scripts\activate.bat` works too\. If you initialized your AWS CDK project using AWS CDK Toolkit v1\.70\.0 or earlier, your virtual environment is in the `.env` directory instead of `.venv`\. 

**Base Infrastructure**

Open the file `./ddb_filters/ddb_filters_stack.py` with your preferred text editor\. This file was auto generated when you created the AWS CDK project\. 

Next, add the functions `_create_ddb_table` and `_set_ddb_trigger_function`\. These functions will create a DynamoDB table with partition key PK and sort key SK in provision mode on\-demand mode, with Amazon DynamoDB Streams enabled by default to show New and Old images\.

The Lambda function will be stored in the folder `lambda` under the file `app.py`\. This file will be created later\. It will include an environment variable `APP_TABLE_NAME`, which will be the name of the Amazon DynamoDB Table created by this stack\. In the same function we will grant stream read permissions to the Lambda function\. Finally, it will subscribe to the DynamoDB Streams as the event source for the lambda function\. 

At the end of the file in the `__init__` method, you will call the respective constructs to initialize them in the stack\. For bigger projects that require additional components and services, it might be best to define these constructs outside the base stack\. 

```
import os
import json

import aws_cdk as cdk
from aws_cdk import (
    Stack,
    aws_lambda as _lambda,
    aws_dynamodb as dynamodb,
)
from constructs import Construct


class DdbFiltersStack(Stack):

    def _create_ddb_table(self):
        dynamodb_table = dynamodb.Table(
            self,
            "AppTable",
            partition_key=dynamodb.Attribute(
                name="PK", type=dynamodb.AttributeType.STRING
            ),
            sort_key=dynamodb.Attribute(
                name="SK", type=dynamodb.AttributeType.STRING),
            billing_mode=dynamodb.BillingMode.PAY_PER_REQUEST,
            stream=dynamodb.StreamViewType.NEW_AND_OLD_IMAGES,
            removal_policy=cdk.RemovalPolicy.DESTROY,
        )

        cdk.CfnOutput(self, "AppTableName", value=dynamodb_table.table_name)
        return dynamodb_table

    def _set_ddb_trigger_function(self, ddb_table):
        events_lambda = _lambda.Function(
            self,
            "LambdaHandler",
            runtime=_lambda.Runtime.PYTHON_3_9,
            code=_lambda.Code.from_asset("lambda"),
            handler="app.handler",
            environment={
                "APP_TABLE_NAME": ddb_table.table_name,
            },
        )

        ddb_table.grant_stream_read(events_lambda)

        event_subscription = _lambda.CfnEventSourceMapping(
            scope=self,
            id="companyInsertsOnlyEventSourceMapping",
            function_name=events_lambda.function_name,
            event_source_arn=ddb_table.table_stream_arn,
            maximum_batching_window_in_seconds=1,
            starting_position="LATEST",
            batch_size=1,
        )

    def __init__(self, scope: Construct, construct_id: str, **kwargs) -> None:
        super().__init__(scope, construct_id, **kwargs)

        ddb_table = self._create_ddb_table()
        self._set_ddb_trigger_function(ddb_table)
```

Now we will create a very simple lambda function that will print the logs into Amazon CloudWatch\. To do this, create a new folder called `lambda`\.

```
mkdir lambda
touch app.py
```

Using your favorite text editor, add the following content to the `app.py` file:

```
import logging

LOGGER = logging.getLogger()
LOGGER.setLevel(logging.INFO)


def handler(event, context):
    LOGGER.info('Received Event: %s', event)
    for rec in event['Records']:
        LOGGER.info('Record: %s', rec)
```

Ensuring you are in the `/ddb_filters/` folder, type the following command to create the sample application:

```
cdk deploy
```

At some point you will be asked to confirm if you want to deploy the solution\. Accept the changes by typing `Y`\.

```
├───┼──────────────────────────────┼────────────────────────────────────────────────────────────────────────────────┤
│ + │ ${LambdaHandler/ServiceRole} │ arn:${AWS::Partition}:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole │
└───┴──────────────────────────────┴────────────────────────────────────────────────────────────────────────────────┘

Do you wish to deploy these changes (y/n)? y

...

✨  Deployment time: 67.73s

Outputs:
DdbFiltersStack.AppTableName = DdbFiltersStack-AppTable815C50BC-1M1W7209V5YPP
Stack ARN:
arn:aws:cloudformation:us-east-2:111122223333:stack/DdbFiltersStack/66873140-40f3-11ed-8e93-0a74f296a8f6
```

Once the changes are deployed, open your AWS console and add one item to your table\. 

```
{
 "PK": "COMPANY#1000",
 "SK": "PRODUCT#CHOCOLATE#DARK",
 "company_id": "1000",
 "type": "",
 "state": "FL",
 "stores": 5,
 "price": 15,
 "quantity": 50,
 "fabric": "Florida Chocolates"
}
```

The CloudWatch logs should now contain all the information from this entry\. 

**Filter Examples**
+ **Only products that matches a given state**

Open the file `ddb_filters/ddb_filters/ddb_filters_stack.py`, and modify it to include the filter that matches all the products that are equals to “FL”\. This can be revised just below the `event_subscription` in line 45\.

```
event_subscription.add_property_override(
    property_path="FilterCriteria",
    value={
        "Filters": [
            {
                "Pattern": json.dumps(
                    {"dynamodb": {"NewImage": {"state": {"S": ["FL"]}}}}
                )
            },
        ]
    },
)
```
+ **Only the items that starts with some values in the PK and SK**

Modify the python script to include the following condition:

```
event_subscription.add_property_override(
    property_path="FilterCriteria",
    value={
        "Filters": [
            {
                "Pattern": json.dumps(
                    {
                        {
                            "dynamodb": {
                                "Keys": {
                                    "PK": {"S": [{"prefix": "COMPANY"}]},
                                    "SK": {"S": [{"prefix": "PRODUCT"}]},
                                }
                            }
                        }
                    }
                )
            },
        ]
    },
```
+ **Either start with some values on PK and SK or is from certain state\.**

Modify the python script to include the following conditions:

```
event_subscription.add_property_override(
    property_path="FilterCriteria",
    value={
        "Filters": [
            {
                "Pattern": json.dumps(
                    {
                        {
                            "dynamodb": {
                                "Keys": {
                                    "PK": {"S": [{"prefix": "COMPANY"}]},
                                    "SK": {"S": [{"prefix": "PRODUCT"}]},
                                }
                            }
                        }
                    }
                )
            },
            {
                "Pattern": json.dumps(
                    {"dynamodb": {"NewImage": {"state": {"S": ["FL"]}}}}
                )
            },
        ]
    },
)
```

Notice that the OR condition is added by adding more elements to the Filters array\.

**Cleanup**

Locate the filter stack in the base of your working directory, and execute `cdk destroy`\. You will be asked to confirm the resource deletion:

```
cdk destroy
Are you sure you want to delete: DdbFiltersStack (y/n)? y
```