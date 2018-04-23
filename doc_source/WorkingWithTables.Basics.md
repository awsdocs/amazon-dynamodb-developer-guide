# Basic Operations for Tables<a name="WorkingWithTables.Basics"></a>

**Topics**
+ [Creating a Table](#WorkingWithTables.Basics.CreateTable)
+ [Describing a Table](#WorkingWithTables.Basics.DescribeTable)
+ [Updating a Table](#WorkingWithTables.Basics.UpdateTable)
+ [Deleting a Table](#WorkingWithTables.Basics.DeleteTable)
+ [Listing Table Names](#WorkingWithTables.Basics.ListTables)
+ [Describing Provisioned Throughput Limits](#WorkingWithTables.Basics.DescribeLimits)

## Creating a Table<a name="WorkingWithTables.Basics.CreateTable"></a>

Use the `CreateTable` operation to create a table\. You must provide the following information:
+ **Table name\.** The name must conform to the DynamoDB naming rules, and must be unique for the current AWS account and region\. For example, you could create a *People* table in US East \(N\. Virginia\) and another *People* table in EU \(Ireland\) \- however, these two tables would be entirely different from each other\. For more information, see [Naming Rules and Data Types](HowItWorks.NamingRulesDataTypes.md)\.
+ **Primary key\.** The primary key can consist of one attribute \(partition key\) or two attributes \(partition key and sort key\)\. You need to provide the attribute names, data types, and the role of each attribute: `HASH` \(for a partition key\) and `RANGE` \(for a sort key\)\. For more information, see [Primary Key](HowItWorks.CoreComponents.md#HowItWorks.CoreComponents.PrimaryKey)\.
+ **Throughput settings\.** You must specify the initial read and write throughput settings for the table\. You can modify these settings later, or enable DynamoDB auto scaling to manage the settings for you\. For more information, see [Throughput Settings for Reads and Writes](ProvisionedThroughput.md) and [Managing Throughput Capacity Automatically with DynamoDB Auto Scaling](AutoScaling.md)\.

**Example**  
The following AWS CLI example shows how to create a table \(*Music*\)\. The primary key consists of *Artist* \(partition key\) and *SongTitle* \(sort key\), each of which has a data type of String\. The maximum throughput for this table is 10 read capacity units and 5 write capacity units\.  

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

The `CreateTable` operation returns metadata for the table, as shown below:

```
{
    "TableDescription": {
        "AttributeDefinitions": [
            {
                "AttributeName": "Artist",
                "AttributeType": "S"
            },
            {
                "AttributeName": "SongTitle",
                "AttributeType": "S"
            }
        ],
        "TableName": "Music",
        "KeySchema": [
            {
                "AttributeName": "Artist",
                "KeyType": "HASH"
            },
            {
                "AttributeName": "SongTitle",
                "KeyType": "RANGE"
            }
        ],
        "TableStatus": "CREATING",
        "CreationDateTime": 1491338657.039,
        "ProvisionedThroughput": {
            "NumberOfDecreasesToday": 0,
            "ReadCapacityUnits": 10,
            "WriteCapacityUnits": 5
        },
        "TableSizeBytes": 0,
        "ItemCount": 0,
        "TableArn": "arn:aws:dynamodb:us-east-1:123456789012:table/Music"
    }
}
```

The `TableStatus` element indicates the current state of the table \(`CREATING`\)\. It might take a while to create the table, depending on the values you specify for `ReadCapacityUnits` and `WriteCapacityUnits`\. Larger values for these will require DynamoDB to allocate more resources for the table\.

## Describing a Table<a name="WorkingWithTables.Basics.DescribeTable"></a>

To view details about a table, use the `DescribeTable` operation\. You must provide the table name\. The output from `DescribeTable` is in the same format as that from `CreateTable`; it includes the timestamp when the table was created, its key schema, its provisioned throughput settings, its estimated size, and any secondary indexes that are present\.

**Example**  

```
aws dynamodb describe-table --table-name Music
```

The table is ready for use when the `TableStatus` has changed from `CREATING` to `ACTIVE`\.

**Note**  
If you issue a `DescribeTable` request immediately after a `CreateTable` request, DynamoDB might return an error \(`ResourceNotFoundException`\)\. This is because `DescribeTable` uses an eventually consistent query, and the metadata for your table might not be available at that moment\. Wait for a few seconds, and then try the `DescribeTable` request again\.  
For billing purposes, your DynamoDB storage costs include a per\-item overhead of 100 bytes\. \(For more information, go to [DynamoDB Pricing](https://aws.amazon.com/dynamodb/pricing/)\)\. This extra 100 bytes per item is not used in capacity unit calculations or by the `DescribeTable` operation\. 

## Updating a Table<a name="WorkingWithTables.Basics.UpdateTable"></a>

The `UpdateTable` operation allows you to do one of the following:
+ Modify a table's provisioned throughput settings\.
+ Manipulate global secondary indexes on the table \(see [Global Secondary Indexes](GSI.md)\)\.
+ Enable or disable DynamoDB Streams on the table \(see [Capturing Table Activity with DynamoDB Streams](Streams.md)\)\.

**Example**  
This AWS CLI example shows how to modify a table's provisioned throughput settings:  

```
aws dynamodb update-table --table-name Music \
    --provisioned-throughput ReadCapacityUnits=20,WriteCapacityUnits=10
```

**Note**  
When you issue an `UpdateTable` request, the status of the table changes from `AVAILABLE` to `UPDATING`\. The table remains fully available for use while it is `UPDATING`\. When this process is completed, the table status changes from `UPDATING` to `AVAILABLE`\.

## Deleting a Table<a name="WorkingWithTables.Basics.DeleteTable"></a>

You can remove an unused table with the `DeleteTable` operation\. Note that deleting a table is an unrecoverable operation\.

This AWS CLI example shows how to delete a table:

```
aws dynamodb delete-table --table-name Music
```

When you issue a `DeleteTable` request, the table's status changes from `ACTIVE` to `DELETING`\. It might take a while to delete the table, depending on the resources it uses \(such as the data stored in the table, and any streams or indexes on the table\)\.

When the `DeleteTable` operation concludes, the table no longer exists in DynamoDB\.

## Listing Table Names<a name="WorkingWithTables.Basics.ListTables"></a>

The `ListTables` operation returns the names of the DynamoDB tables for the current AWS account and region\.

**Example**  

```
aws dynamodb list-tables
```

## Describing Provisioned Throughput Limits<a name="WorkingWithTables.Basics.DescribeLimits"></a>

The `DescribeLimits` operation returns the current read and write capacity limits for the current AWS account and region\.

**Example**  

```
aws dynamodb describe-limits
```
The output shows the upper limits of read and write capacity units for the current AWS account and region\.

For more information about these limits, and how to request limit increases, see [Provisioned Throughput Default Limits](Limits.md#default-limits-throughput)\.