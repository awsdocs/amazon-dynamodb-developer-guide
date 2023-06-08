# Using the AWS CLI<a name="Tools.CLI"></a>

 You can use the AWS Command Line Interface \(AWS CLI\) to control multiple AWS services from the command line and automate them through scripts\. You can use the AWS CLI for ad hoc operations, such as creating a table\. You can also use it to embed Amazon DynamoDB operations within utility scripts\.

 Before you can use the AWS CLI with DynamoDB, you must get an access key ID and secret access key\. For more information, see [Granting programmatic access](SettingUp.DynamoWebService.md#SettingUp.DynamoWebService.GetCredentials)\. 

For a complete listing of all the commands available for DynamoDB in the AWS CLI, see the [AWS CLI command reference](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/index.html)\.

**Topics**
+ [Downloading and configuring the AWS CLI](#Tools.CLI.DownloadingAndRunning)
+ [Using the AWS CLI with DynamoDB](#Tools.CLI.UsingWithDDB)
+ [Using the AWS CLI with downloadable DynamoDB](#Tools.CLI.UsingWithDDBLocal)

## Downloading and configuring the AWS CLI<a name="Tools.CLI.DownloadingAndRunning"></a>

The AWS CLI is available at [http://aws\.amazon\.com/cli](https://aws.amazon.com/cli)\. It runs on Windows, macOS, or Linux\. After you download the AWS CLI, follow these steps to install and configure it:

1. Go to the [AWS Command Line Interface User Guide](https://docs.aws.amazon.com/cli/latest/userguide/)\.

1. Follow the instructions for [Installing the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/installing.html) and [Configuring the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html)\.

## Using the AWS CLI with DynamoDB<a name="Tools.CLI.UsingWithDDB"></a>

The command line format consists of a DynamoDB operation name followed by the parameters for that operation\. The AWS CLI supports a shorthand syntax for the parameter values, as well as JSON\.

For example, the following command creates a table named *Music*\. The partition key is *Artist*, and the sort key is *SongTitle*\. \(For easier readability, long commands in this section are broken into separate lines\.\)

```
aws dynamodb create-table \
    --table-name Music \
    --attribute-definitions \
        AttributeName=Artist,AttributeType=S \
        AttributeName=SongTitle,AttributeType=S \
    --key-schema AttributeName=Artist,KeyType=HASH AttributeName=SongTitle,KeyType=RANGE \
    --provisioned-throughput ReadCapacityUnits=1,WriteCapacityUnits=1 \
    --table-class STANDARD
```

The following commands add new items to the table\. These examples use a combination of shorthand syntax and JSON\.

```
aws dynamodb put-item \
    --table-name Music \
    --item \
        '{"Artist": {"S": "No One You Know"}, "SongTitle": {"S": "Call Me Today"}, "AlbumTitle": {"S": "Somewhat Famous"}}' \
    --return-consumed-capacity TOTAL  

aws dynamodb put-item \
    --table-name Music \
    --item '{
        "Artist": {"S": "Acme Band"},
        "SongTitle": {"S": "Happy Day"},
        "AlbumTitle": {"S": "Songs About Life"} }' \
    --return-consumed-capacity TOTAL
```

On the command line, it can be difficult to compose valid JSON\. However, the AWS CLI can read JSON files\. For example, consider the following JSON code snippet, which is stored in a file named *key\-conditions\.json*\.

```
{
    "Artist": {
        "AttributeValueList": [
            {   
                "S": "No One You Know"
            }   
        ],  
        "ComparisonOperator": "EQ"
    },  
    "SongTitle": {
        "AttributeValueList": [
            {   
                "S": "Call Me Today"
            }   
        ],  
        "ComparisonOperator": "EQ"
    }
}
```

You can now issue a `Query` request using the AWS CLI\. In this example, the contents of the *key\-conditions\.json* file are used for the `--key-conditions` parameter\.

```
aws dynamodb query --table-name Music --key-conditions file://key-conditions.json
```

## Using the AWS CLI with downloadable DynamoDB<a name="Tools.CLI.UsingWithDDBLocal"></a>

The AWS CLI can also interact with DynamoDB \(Downloadable Version\) that runs on your computer\. To enable this, add the following parameter to each command:

`--endpoint-url http://localhost:8000`

The following example uses the AWS CLI to list the tables in a local database\.

```
aws dynamodb list-tables --endpoint-url http://localhost:8000
```

If DynamoDB is using a port number other than the default \(8000\), modify the `--endpoint-url` value accordingly\.

**Note**  
The AWS CLI can't use the downloadable version of DynamoDB as a default endpoint\. Therefore, you must specify `--endpoint-url` with each command\. 