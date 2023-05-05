# Cheat sheet for DynamoDB<a name="CheatSheet"></a>

This cheat sheet provides a quick reference for working with Amazon DynamoDB and its various AWS SDKs\.

## Initial setup<a name="CheatSheet.InitialSetup"></a>

1. [Sign up for AWS](SettingUp.DynamoWebService.html#SettingUp.DynamoWebService.SignUpForAWS)\.

1. [Get an AWS access key](SettingUp.DynamoWebService.html#SettingUp.DynamoWebService.GetCredentials) to access DynamoDB programmatically\.

1. [Configure your DynamoDB credentials](SettingUp.DynamoWebService.html#SettingUp.DynamoWebService.ConfigureCredentials)\.

**See also:**
+ [Setting up DynamoDB \(web service\)](SettingUp.DynamoWebService.html)
+ [Getting started with DynamoDB](GettingStartedDynamoDB.html)
+ [Basic overview of core components](HowItWorks.CoreComponents.html)

 

## SDK or CLI<a name="CheatSheet.Platform"></a>

Choose your preferred [SDK](sdk-general-information-section.html), or set up the [AWS CLI](/cli/latest/index.html)\.

**Note**  
When you use the AWS CLI on Windows, a backslash \(\\\) that is not inside a quote is treated as a carriage return\. Also, you must escape any quotes and braces inside other quotes\. For an example, see the **Windows** tab in "Create a table" in the next section\.

**See also:**
+ [AWS CLI with DynamoDB](Tools.CLI.html)
+ [Getting started with DynamoDB \- step 2](getting-started-step-2.html)

## Basic actions<a name="CheatSheet.BasicActions"></a>

This section provides code for basic DynamoDB tasks\. For more information about these tasks, see [Getting started with DynamoDB and the AWS SDKs](GettingStarted.html)\.

### Create a table<a name="CheatSheet.BasicActions.CreateTable"></a>

------
#### [ Default ]

```
aws dynamodb create-table \
    --table-name Music \
    --attribute-definitions \
        AttributeName=Artist,AttributeType=S \
        AttributeName=SongTitle,AttributeType=S \
    --key-schema \
        AttributeName=Artist,KeyType=HASH \
        AttributeName=SongTitle,KeyType=RANGE \
    --provisioned-throughput \
        ReadCapacityUnits=10,WriteCapacityUnits=5
```

------
#### [ Windows ]

```
aws dynamodb put-item --table-name Music --item "{\"Artist\": {\"S\": \"No One You Know\"}, \"SongTitle\": {\"S\": \"Call Me Today\"}, \"AlbumTitle\": {\"S\": \"Somewhat Famous\"}, \"Awards\": {\"N\": \"1\"}}"
```

------

### Write item to a table<a name="CheatSheet.BasicActions.WriteItem"></a>

```
aws dynamodb put-item \ --table-name Music \ --item file://item.json
```

### Read item from a table<a name="CheatSheet.BasicActions.ReadItem"></a>

```
aws dynamodb get-item \ --table-name Music \ --item file://item.json
```

### Delete item from a table<a name="CheatSheet.BasicActions.DeleteItem"></a>

```
aws dynamodb delete-item --table-name Music --key file://key.json
```

### Query a table<a name="CheatSheet.BasicActions.QueryTable"></a>

```
aws dynamodb query --table-name Music 
--key-condition-expression "ArtistName=:Artist and SongName=:Songtitle"
```

### Delete a table<a name="CheatSheet.BasicActions.DeleteTable"></a>

```
aws dynamodb delete-table --table-name Music
```

### List table names<a name="CheatSheet.BasicActions.ListTableNames"></a>

```
aws dynamodb list-tables
```

## Naming rules<a name="CheatSheet.NamingRules"></a>
+ All names must be encoded using UTF\-8 and are case sensitive\.
+ Table names and index names must be between 3 and 255 characters long, and can contain only the following characters:
  + `a-z`
  + `A-Z`
  + `0-9`
  + `_`\(underscore\)
  + `-`\(dash\)
  + `.`\(dot\)
+ Attribute names must be at least one character long, and less than 64 KB in size\.

For more information, see [Naming rules](HowItWorks.NamingRulesDataTypes.html)\.

## Service quota basics<a name="CheatSheet.ServiceBasics"></a>

**Read and write units**
+ **Read capacity unit \(RCU\)** – One strongly consistent read per second, or two eventually consistent reads per second, for items up to 4 KB in size\.
+ **Write capacity unit \(WCU\)** – One write per second, for items up to 1 KB in size\.

**Table limits**
+ **Table size** – There is no practical limit on table size\. Tables are unconstrained in terms of the number of items or the number of bytes\.
+ **Number of tables** – For any AWS account, there is an initial quota of 2,500 tables per AWS Region\. 
+ **Page size limit for query and scan** – There is a limit of 1 MB per page, per query or scan\. If your query parameters or scan operation on a table result in more than 1 MB of data, DynamoDB returns the initial matching items\. It also returns a `LastEvaluatedKey` property that you can use in a new request to read the next page\.

**Indexes**
+ **Local secondary indexes \(LSIs\)** – You can define a maximum of five local secondary indexes\. LSIs are primarily useful when an index must have strong consistency with the base table\. 
+ **Global secondary indexes \(GSIs\)** – There is a default quota of 20 global secondary indexes per table\.
+ **Projected secondary index attributes per table** – You can project a total of up to 100 attributes into all of a table's local and global secondary indexes\. This only applies to user\-specified projected attributes\.

**Partition keys**
+ The minimum length of a partition key value is 1 byte\. The maximum length is 2048 bytes\.
+ There is no practical limit on the number of distinct partition key values, for tables or for secondary indexes\.
+ The minimum length of a sort key value is 1 byte\. The maximum length is 1024 bytes\.
+ In general, there is no practical limit on the number of distinct sort key values per partition key value\. The exception is for tables with secondary indexes\.

For more information on secondary indexes, partition key design, and sort key design, see [Best practices](best-practices.html)\.

**Limits for commonly used data types**
+ **String** – The length of a string is constrained by the maximum item size of 400 KB\. Strings are Unicode with UTF\-8 binary encoding\.
+ **Number** – A number can have up to 38 digits of precision, and can be positive, negative, or zero\.
+ **Binary** – The length of a binary is constrained by the maximum item size of 400 KB\. Applications that work with binary attributes must encode the data in base64 encoding before sending it to DynamoDB\.

For a full list of supported data types, see [Data types](HowItWorks.NamingRulesDataTypes.html#HowItWorks.DataTypes)\. For more information, also see [Service quotas](ServiceQuotas.html#limits-items)\.

### Items, attributes, and expression parameters<a name="CheatSheet.ServiceBasics.Misc"></a>

The maximum item size in DynamoDB is 400 KB, which includes both attribute name binary length \(UTF\-8 length\) and attribute value binary lengths \(UTF\-8 length\)\. The attribute name counts towards the size limit\.

There is no limit on the number of values in a list, map, or set, as long as the item that contains the values fits within the 400\-KB item size limit\.

For expression parameters, the maximum length of any expression string is 4 KB\.

For more information about item size, attributes, and expression parameters, see [Service quotas](ServiceQuotas.html#limits-items)\.

## More information<a name="CheatSheet.FurtherInfo"></a>
+ [Security](security.html)
+ [Monitoring and logging](monitoring.html)
+ [Working with streams](streamsmain.html)
+ [Backups](BackupRestore.html) and [Point\-in\-time recovery](PointInTimeRecovery.html)
+ [Integrating with other AWS services](OtherServices.html) 
+ [API reference](/amazondynamodb/latest/APIReference/Welcome.html)
+ [Architecture Center: Database Best Practices](https://aws.amazon.com/architecture/databases/)
+ [Video tutorials](https://youtu.be/Mw8wCj0gkRc)
+ [DynamoDB forum](https://repost.aws/search/questions?globalSearch=dynamodb)