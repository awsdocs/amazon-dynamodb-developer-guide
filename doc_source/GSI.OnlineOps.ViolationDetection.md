# Detecting and Correcting Index Key Violations<a name="GSI.OnlineOps.ViolationDetection"></a>

During the backfill phase of global secondary index creation, Amazon DynamoDB examines each item in the table to determine whether it is eligible for inclusion in the index\. Some items might not be eligible because they would cause index key violations\. In these cases, the items remain in the table, but the index doesn't have a corresponding entry for that item\.

An *index key violation* occurs in the following situations:
+ There is a data type mismatch between an attribute value and the index key schema data type\. For example, suppose that one of the items in the `GameScores` table had a `TopScore` value of type `String`\. If you added a global secondary index with a partition key of `TopScore`, of type `Number`, the item from the table would violate the index key\.
+ An attribute value from the table exceeds the maximum length for an index key attribute\. The maximum length of a partition key is 2048 bytes, and the maximum length of a sort key is 1024 bytes\. If any of the corresponding attribute values in the table exceed these limits, the item from the table would violate the index key\.

**Note**  
If a String or Binary attribute value is set for an attribute that is used as an index key, then the attribute value must have a length greater than zero;, otherwise, the item from the table would violate the index key\.  
This tool does not flag this index key violation, at this time\.

If an index key violation occurs, the backfill phase continues without interruption\. However, any violating items are not included in the index\. After the backfill phase completes, all writes to items that violate the new index's key schema will be rejected\.

To identify and fix attribute values in a table that violate an index key, use the Violation Detector tool\. To run Violation Detector, you create a configuration file that specifies the name of a table to be scanned, the names and data types of the global secondary index partition key and sort key, and what actions to take if any index key violations are found\. Violation Detector can run in one of two different modes:
+ **Detection mode** — Detect index key violations\. Use detection mode to report the items in the table that would cause key violations in a global secondary index\. \(You can optionally request that these violating table items be deleted immediately when they are found\.\) The output from detection mode is written to a file, which you can use for further analysis\.
+ **Correction mode** — Correct index key violations\. In correction mode, Violation Detector reads an input file with the same format as the output file from detection mode\. Correction mode reads the records from the input file and, for each record, it either deletes or updates the corresponding items in the table\. \(Note that if you choose to update the items, you must edit the input file and set appropriate values for these updates\.\)

## Downloading and Running Violation Detector<a name="GSI.OnlineOps.ViolationDetection.Running"></a>

Violation Detector is available as an executable Java Archive \(`.jar` file\), and runs on Windows, macOS, or Linux computers\. Violation Detector requires Java 1\.7 \(or later\) and Apache Maven\.
+ [Download Violation Detector from GitHub](https://github.com/awslabs/dynamodb-online-index-violation-detector)

Follow the instructions in the `README.md` file to download and install Violation Detector using Maven\.

To start Violation Detector, go to the directory where you have built `ViolationDetector.java` and enter the following command\.

```
java -jar ViolationDetector.jar [options]
```

The Violation Detector command line accepts the following options:
+ `-h | --help` — Prints a usage summary and options for Violation Detector\.
+ `-p | --configFilePath` `value` — The fully qualified name of a Violation Detector configuration file\. For more information, see [The Violation Detector Configuration File](#GSI.OnlineOps.ViolationDetection.ConfigFile)\.
+ `-t | --detect` `value` — Detect index key violations in the table, and write them to the Violation Detector output file\. If the value of this parameter is set to `keep`, items with key violations are not modified\. If the value is set to `delete`, items with key violations are deleted from the table\.
+ `-c | --correct` `value` — Read index key violations from an input file, and take corrective actions on the items in the table\. If the value of this parameter is set to `update`, items with key violations are updated with new, non\-violating values\. If the value is set to `delete`, items with key violations are deleted from the table\.

## The Violation Detector Configuration File<a name="GSI.OnlineOps.ViolationDetection.ConfigFile"></a>

At runtime, the Violation Detector tool requires a configuration file\. The parameters in this file determine which DynamoDB resources that Violation Detector can access, and how much provisioned throughput it can consume\. The following table describes these parameters\.


****  

| Parameter Name | Description | Required? | 
| --- | --- | --- | 
|  `awsCredentialsFile`  |  The fully qualified name of a file containing your AWS credentials\. The credentials file must be in the following format: <pre>accessKey = access_key_id_goes_here<br />secretKey = secret_key_goes_here </pre>  |  Yes  | 
|  `dynamoDBRegion`  |  The AWS Region in which the table resides\. For example: `us-west-2`\.  |  Yes  | 
|  `tableName`  | The name of the DynamoDB table to be scanned\. |  Yes  | 
|  `gsiHashKeyName`  |  The name of the index partition key\.  |  Yes  | 
|  `gsiHashKeyType`  |  The data type of the index partition key—`String`, `Number`, or `Binary`: `S \| N \| B`  |  Yes  | 
|  `gsiRangeKeyName`  |  The name of the index sort key\. Do not specify this parameter if the index only has a simple primary key \(partition key\)\.  |  No  | 
|  `gsiRangeKeyType`  |  The data type of the index sort key—`String`, `Number`, or `Binary`: `S \| N \| B`  Do not specify this parameter if the index only has a simple primary key \(partition key\)\.  |  No  | 
|  `recordDetails`  |  Whether to write the full details of index key violations to the output file\. If set to `true` \(the default\), full information about the violating items is reported\. If set to `false`, only the number of violations is reported\.  |  No  | 
|  `recordGsiValueInViolationRecord`  |  Whether to write the values of the violating index keys to the output file\. If set to `true` \(default\), the key values are reported\. If set to `false`, the key values are not reported\.  |  No  | 
|  `detectionOutputPath`  |  The full path of the Violation Detector output file\. This parameter supports writing to a local directory or to Amazon Simple Storage Service \(Amazon S3\)\. The following are examples: `detectionOutputPath = ``//local/path/filename.csv` `detectionOutputPath = ``s3://bucket/filename.csv` Information in the output file appears in comma\-separated values \(CSV\) format\. If you don't set `detectionOutputPath`, the output file is named `violation_detection.csv` and is written to your current working directory\.  |  No  | 
|  `numOfSegments`  | The number of parallel scan segments to be used when Violation Detector scans the table\. The default value is 1, meaning that the table is scanned in a sequential manner\. If the value is 2 or higher, then Violation Detector divides the table into that many logical segments and an equal number of scan threads\. The maximum setting for `numOfSegments` is 4096\.For larger tables, a parallel scan is generally faster than a sequential scan\. In addition, if the table is large enough to span multiple partitions, a parallel scan distributes its read activity evenly across multiple partitions\.For more information about parallel scans in DynamoDB, see [Parallel Scan](Scan.md#Scan.ParallelScan)\. |  No  | 
|  `numOfViolations`  |  The upper limit of index key violations to write to the output file\. If set to `-1` \(the default\), the entire table is scanned\. If set to a positive integer, then Violation Detector stops after it encounters that number of violations\.  |  No  | 
|  `numOfRecords`  |  The number of items in the table to be scanned\. If set to \-1 \(the default\), the entire table is scanned\. If set to a positive integer, Violation Detector stops after it scans that many items in the table\.  |  No  | 
|  `readWriteIOPSPercent`  |  Regulates the percentage of provisioned read capacity units that are consumed during the table scan\. Valid values range from `1` to `100`\. The default value \(`25`\) means that Violation Detector will consume no more than 25% of the table's provisioned read throughput\.  |  No  | 
|  `correctionInputPath`  |  The full path of the Violation Detector correction input file\. If you run Violation Detector in correction mode, the contents of this file are used to modify or delete data items in the table that violate the global secondary index\. The format of the `correctionInputPath` file is the same as that of the `detectionOutputPath` file\. This lets you process the output from detection mode as input in correction mode\.  |  No  | 
|  `correctionOutputPath`  |  The full path of the Violation Detector correction output file\. This file is created only if there are update errors\. This parameter supports writing to a local directory or to Amazon S3\. The following are examples: `correctionOutputPath = ``//local/path/filename.csv` `correctionOutputPath = ``s3://bucket/filename.csv` Information in the output file appears in CSV format\. If you don't set `correctionOutputPath`, the output file is named `violation_update_errors.csv` and is written to your current working directory\.  |  No  | 

## Detection<a name="GSI.OnlineOps.ViolationDetection.Detection"></a>

To detect index key violations, use Violation Detector with the `--detect` command line option\. To show how this option works, consider the `ProductCatalog` table shown in [Creating Tables and Loading Data for Code Examples in DynamoDB](SampleData.md)\. The following is a list of items in the table\. Only the primary key \(`Id`\) and the `Price` attribute are shown\.


****  

| Id \(Primary Key\) | Price | 
| --- | --- | 
| 101 |  5  | 
| 102 |  20  | 
| 103 | 200  | 
| 201 |  100  | 
| 202 |  200  | 
| 203 |  300  | 
| 204 |  400  | 
| 205 |  500  | 

All of the values for `Price` are of type `Number`\. However, because DynamoDB is schemaless, it is possible to add an item with a non\-numeric `Price`\. For example, suppose that you add another item to the `ProductCatalog` table\.


****  

| Id \(Primary Key\) | Price | 
| --- | --- | 
| 999 | "Hello" | 

The table now has a total of nine items\.

Now you add a new global secondary index to the table: `PriceIndex`\. The primary key for this index is a partition key, `Price`, which is of type `Number`\. After the index has been built, it will contain eight items—but the `ProductCatalog` table has nine items\. The reason for this discrepancy is that the value `"Hello"` is of type `String`, but `PriceIndex` has a primary key of type `Number`\. The `String` value violates the global secondary index key, so it is not present in the index\.

To use Violation Detector in this scenario, you first create a configuration file such as the following\.

```
# Properties file for violation detection tool configuration.
# Parameters that are not specified will use default values.

awsCredentialsFile = /home/alice/credentials.txt
dynamoDBRegion = us-west-2
tableName = ProductCatalog
gsiHashKeyName = Price
gsiHashKeyType = N
recordDetails = true
recordGsiValueInViolationRecord = true
detectionOutputPath = ./gsi_violation_check.csv
correctionInputPath = ./gsi_violation_check.csv
numOfSegments = 1
readWriteIOPSPercent = 40
```

Next, you run Violation Detector as in the following example\.

```
$  java -jar ViolationDetector.jar --configFilePath config.txt --detect keep

Violation detection started: sequential scan, Table name: ProductCatalog, GSI name: PriceIndex
Progress: Items scanned in total: 9,    Items scanned by this thread: 9,    Violations found by this thread: 1, Violations deleted by this thread: 0
Violation detection finished: Records scanned: 9, Violations found: 1, Violations deleted: 0, see results at: ./gsi_violation_check.csv
```

If the `recordDetails` config parameter is set to `true`, Violation Detector writes details of each violation to the output file, as in the following example\.

```
Table Hash Key,GSI Hash Key Value,GSI Hash Key Violation Type,GSI Hash Key Violation Description,GSI Hash Key Update Value(FOR USER),Delete Blank Attributes When Updating?(Y/N) 

999,"{""S"":""Hello""}",Type Violation,Expected: N Found: S,,
```

The output file is in CSV format\. The first line in the file is a header, followed by one record per item that violates the index key\. The fields of these violation records are as follows:
+ **Table Hash Key** — The partition key value of the item in the table\.
+ **Table Range Key** — The sort key value of the item in the table\.
+ **GSI Hash Key Value** — The partition key value of the global secondary index\.
+ **GSI Hash Key Violation Type** — Either `Type Violation` or `Size Violation`\.
+ **GSI Hash Key Violation Description** — The cause of the violation\.
+ **GSI Hash Key Update Value\(FOR USER\)** — In correction mode, a new user\-supplied value for the attribute\.
+ **GSI Range Key Value** — The sort key value of the global secondary index\.
+ **GSI Range Key Violation Type** — Either `Type Violation` or `Size Violation`\.
+ **GSI Range Key Violation Description** — The cause of the violation\.
+ **GSI Range Key Update Value\(FOR USER\)** — In correction mode, a new user\-supplied value for the attribute\.
+ **Delete Blank Attribute When Updating\(Y/N\)** — In correction mode, determines whether to delete \(Y\) or keep \(N\) the violating item in the table—but only if either of the following fields are blank:
  + `GSI Hash Key Update Value(FOR USER)`
  + `GSI Range Key Update Value(FOR USER)`

  If either of these fields are non\-blank, then `Delete Blank Attribute When Updating(Y/N)` has no effect\.

**Note**  
The output format might vary, depending on the configuration file and command line options\. For example, if the table has a simple primary key \(without a sort key\), no sort key fields will be present in the output\.  
The violation records in the file might not be in sorted order\.

## Correction<a name="GSI.OnlineOps.ViolationDetection.Correction"></a>

To correct index key violations, use Violation Detector with the `--correct` command line option\. In correction mode, Violation Detector reads the input file specified by the `correctionInputPath` parameter\. This file has the same format as the `detectionOutputPath` file, so that you can use the output from detection as input for correction\.

Violation Detector provides two different ways to correct index key violations:
+ **Delete violations** — Delete the table items that have violating attribute values\.
+ **Update violations** — Update the table items, replacing the violating attributes with non\-violating values\.

In either case, you can use the output file from detection mode as input for correction mode\.

Continuing with the `ProductCatalog` example, suppose that you want to delete the violating item from the table\. To do this, you use the following command line\.

```
$  java -jar ViolationDetector.jar --configFilePath config.txt --correct delete
```

At this point, you are asked to confirm whether you want to delete the violating items\.

```
Are you sure to delete all violations on the table?y/n
y
Confirmed, will delete violations on the table...
Violation correction from file started: Reading records from file: ./gsi_violation_check.csv, will delete these records from table.
Violation correction from file finished: Violations delete: 1, Violations Update: 0
```

Now both `ProductCatalog` and `PriceIndex` have the same number of items\.