# Copying Data Between DynamoDB and Amazon S3<a name="EMRforDynamoDB.CopyingData.S3"></a>

If you have data in a DynamoDB table, you can use Hive to copy the data to an Amazon S3 bucket\.

You might do this if you want to create an archive of data in your DynamoDB table\. For example, suppose you have a test environment where you need to work with a baseline set of test data in DynamoDB\. You can copy the baseline data to an Amazon S3 bucket, and then run your tests\. Afterward, you can reset the test environment by restoring the baseline data from the Amazon S3 bucket to DynamoDB\.

If you worked through [Tutorial: Working with Amazon DynamoDB and Apache Hive](EMRforDynamoDB.Tutorial.md), then you already have an Amazon S3 bucket that contains your Amazon EMR logs\. You can use this bucket for the examples in this section, if you know the root path for the bucket:

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. For **Name**, choose your cluster\.

1. The URI is listed in **Log URI** under **Configuration Details**\.

1. Make a note of the root path of the bucket\. The naming convention is:

   `s3://aws-logs-accountID-region`

   where *accountID* is your AWS account ID and region is the AWS region for the bucket\.

**Note**  
For these examples, we will use a subpath within the bucket, as in this example:  
 `s3://aws-logs-123456789012-us-west-2/hive-test`

The following procedures are written with the assumption you followed the steps in the tutorial and have an external table in DynamoDB named *ddb\_features*\.

**Topics**
+ [Copying Data Using the Hive Default Format](#EMRforDynamoDB.CopyingData.S3.DefaultFormat)
+ [Copying Data with a User\-Specified Format](#EMRforDynamoDB.CopyingData.S3.UserSpecifiedFormat)
+ [Copying Data Without a Column Mapping](#EMRforDynamoDB.CopyingData.S3.NoColumnMapping)
+ [Viewing the Data in Amazon S3](#EMRforDynamoDB.CopyingData.S3.ViewingData)

## Copying Data Using the Hive Default Format<a name="EMRforDynamoDB.CopyingData.S3.DefaultFormat"></a>

**Example From DynamoDB to Amazon S3**  
Use an `INSERT OVERWRITE` statement to write directly to Amazon S3\.  

```
INSERT OVERWRITE DIRECTORY 's3://aws-logs-123456789012-us-west-2/hive-test'
SELECT * FROM ddb_features;
```
The data file in Amazon S3 looks like this:  

```
920709^ASoldiers Farewell Hill^ASummit^ANM^A32.3564729^A-108.33004616135
1178153^AJones Run^AStream^APA^A41.2120086^A-79.25920781260
253838^ASentinel Dome^ASummit^ACA^A37.7229821^A-119.584338133
264054^ANeversweet Gulch^AValley^ACA^A41.6565269^A-122.83614322900
115905^AChacaloochee Bay^ABay^AAL^A30.6979676^A-87.97388530
```
Each field is separated by an SOH character \(start of heading, 0x01\)\. In the file, SOH appears as **^A**\.

**Example From Amazon S3 to DynamoDB**  

1. Create an external table pointing to the unformatted data in Amazon S3\.

   ```
   CREATE EXTERNAL TABLE s3_features_unformatted
       (feature_id       BIGINT,
       feature_name      STRING ,
       feature_class     STRING ,
       state_alpha       STRING,
       prim_lat_dec      DOUBLE ,
       prim_long_dec     DOUBLE ,
       elev_in_ft        BIGINT)
   LOCATION 's3://aws-logs-123456789012-us-west-2/hive-test';
   ```

1. Copy the data to DynamoDB\.

   ```
   INSERT OVERWRITE TABLE ddb_features
   SELECT * FROM s3_features_unformatted;
   ```

## Copying Data with a User\-Specified Format<a name="EMRforDynamoDB.CopyingData.S3.UserSpecifiedFormat"></a>

If you want to specify your own field separator character, you can create an external table that maps to the Amazon S3 bucket\. You might use this technique for creating data files with comma\-separated values \(CSV\)\.

**Example From DynamoDB to Amazon S3**  

1. Create a Hive external table that maps to Amazon S3\. When you do this, ensure that the data types are consistent with those of the DynamoDB external table\.

   ```
   CREATE EXTERNAL TABLE s3_features_csv
       (feature_id       BIGINT,
       feature_name      STRING,
       feature_class     STRING,
       state_alpha       STRING,
       prim_lat_dec      DOUBLE,
       prim_long_dec     DOUBLE,
       elev_in_ft        BIGINT)
   ROW FORMAT DELIMITED
   FIELDS TERMINATED BY ','
   LOCATION 's3://aws-logs-123456789012-us-west-2/hive-test';
   ```

1. Copy the data from DynamoDB\.

   ```
   INSERT OVERWRITE TABLE s3_features_csv
   SELECT * FROM ddb_features;
   ```
The data file in Amazon S3 looks like this:  

```
920709,Soldiers Farewell Hill,Summit,NM,32.3564729,-108.3300461,6135
1178153,Jones Run,Stream,PA,41.2120086,-79.2592078,1260
253838,Sentinel Dome,Summit,CA,37.7229821,-119.58433,8133
264054,Neversweet Gulch,Valley,CA,41.6565269,-122.8361432,2900
115905,Chacaloochee Bay,Bay,AL,30.6979676,-87.9738853,0
```

**Example From Amazon S3 to DynamoDB**  
With a single HiveQL statement, you can populate the DynamoDB table using the data from Amazon S3:  

```
INSERT OVERWRITE TABLE ddb_features
SELECT * FROM s3_features_csv;
```

## Copying Data Without a Column Mapping<a name="EMRforDynamoDB.CopyingData.S3.NoColumnMapping"></a>

You can copy data from DynamoDB in a raw format and write it to Amazon S3 without specifying any data types or column mapping\. You can use this method to create an archive of DynamoDB data and store it in Amazon S3\.



**Note**  
If your DynamoDB table contains attributes of type Map, List, Boolean or Null, then this is the only way you can use Hive to copy data from DynamoDB to Amazon S3\.

**Example From DynamoDB to Amazon S3**  

1. Create an external table associated with your DynamoDB table\. \(There is no `dynamodb.column.mapping` in this HiveQL statement\.\)

   ```
   CREATE EXTERNAL TABLE ddb_features_no_mapping
       (item MAP<STRING, STRING>)
   STORED BY 'org.apache.hadoop.hive.dynamodb.DynamoDBStorageHandler'
   TBLPROPERTIES ("dynamodb.table.name" = "Features");
   ```

   

1. Create another external table associated with your Amazon S3 bucket\.

   ```
   CREATE EXTERNAL TABLE s3_features_no_mapping
       (item MAP<STRING, STRING>)
   ROW FORMAT DELIMITED
   FIELDS TERMINATED BY '\t'
   LINES TERMINATED BY '\n'
   LOCATION 's3://aws-logs-123456789012-us-west-2/hive-test';
   ```

1. Copy the data from DynamoDB to Amazon S3\.

   ```
   INSERT OVERWRITE TABLE s3_features_no_mapping
   SELECT * FROM ddb_features_no_mapping;
   ```
The data file in Amazon S3 looks like this:  

```
Name^C{"s":"Soldiers Farewell Hill"}^BState^C{"s":"NM"}^BClass^C{"s":"Summit"}^BElevation^C{"n":"6135"}^BLatitude^C{"n":"32.3564729"}^BId^C{"n":"920709"}^BLongitude^C{"n":"-108.3300461"}
Name^C{"s":"Jones Run"}^BState^C{"s":"PA"}^BClass^C{"s":"Stream"}^BElevation^C{"n":"1260"}^BLatitude^C{"n":"41.2120086"}^BId^C{"n":"1178153"}^BLongitude^C{"n":"-79.2592078"}
Name^C{"s":"Sentinel Dome"}^BState^C{"s":"CA"}^BClass^C{"s":"Summit"}^BElevation^C{"n":"8133"}^BLatitude^C{"n":"37.7229821"}^BId^C{"n":"253838"}^BLongitude^C{"n":"-119.58433"}
Name^C{"s":"Neversweet Gulch"}^BState^C{"s":"CA"}^BClass^C{"s":"Valley"}^BElevation^C{"n":"2900"}^BLatitude^C{"n":"41.6565269"}^BId^C{"n":"264054"}^BLongitude^C{"n":"-122.8361432"}
Name^C{"s":"Chacaloochee Bay"}^BState^C{"s":"AL"}^BClass^C{"s":"Bay"}^BElevation^C{"n":"0"}^BLatitude^C{"n":"30.6979676"}^BId^C{"n":"115905"}^BLongitude^C{"n":"-87.9738853"}
```
Each field begins with an STX character \(start of text, 0x02\) and ends with an ETX character \(end of text, 0x03\)\. In the file, STX appears as **^B** and ETX appears as **^C**\.

**Example From Amazon S3 to DynamoDB**  
With a single HiveQL statement, you can populate the DynamoDB table using the data from Amazon S3:  

```
INSERT OVERWRITE TABLE ddb_features_no_mapping
SELECT * FROM s3_features_no_mapping;
```

## Viewing the Data in Amazon S3<a name="EMRforDynamoDB.CopyingData.S3.ViewingData"></a>

If you use SSH to connect to the leader node, you can use the AWS Command Line Interface \(AWS CLI\) to access the data that Hive wrote to Amazon S3\.

The following steps are written with the assumption you have copied data from DynamoDB to Amazon S3 using one of the procedures in this section\.

1. If you are currently at the Hive command prompt, exit to the Linux command prompt\.

   ```
   hive> exit;
   ```

1. List the contents of the hive\-test directory in your Amazon S3 bucket\. \(This is where Hive copied the data from DynamoDB\.\)

   ```
   aws s3 ls s3://aws-logs-123456789012-us-west-2/hive-test/
   ```

   The response should look similar to this:

   `2016-11-01 23:19:54 81983 000000_0` 

   The file name \(*000000\_0*\) is system\-generated\.

1. \(Optional\) You can copy the data file from Amazon S3 to the local file system on the leader node\. After you do this, you can use standard Linux command line utilities to work with the data in the file\.

   ```
   aws s3 cp s3://aws-logs-123456789012-us-west-2/hive-test/000000_0 .
   ```

   The response should look similar to this:

   `download: s3://aws-logs-123456789012-us-west-2/hive-test/000000_0 to ./000000_0`
**Note**  
The local file system on the leader node has limited capacity\. Do not use this command with files that are larger than the available space in the local file system\.