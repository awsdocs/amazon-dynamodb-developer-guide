# Copying Data Between DynamoDB and HDFS<a name="EMRforDynamoDB.CopyingData.HDFS"></a>

If you have data in a DynamoDB table, you can use Hive to copy the data to the Hadoop Distributed File System \(HDFS\)\.

You might do this if you are running a MapReduce job that requires data from DynamoDB\. If you copy the data from DynamoDB into HDFS, Hadoop can process it, using all of the available nodes in the Amazon EMR cluster in parallel\. When the MapReduce job is complete, you can then write the results from HDFS to DDB\.

In the following examples, Hive will read from and write to the following HDFS directory: `/user/hadoop/hive-test`

**Note**  
The examples in this section are written with the assumption you followed the steps in [Tutorial: Working with Amazon DynamoDB and Apache Hive](EMRforDynamoDB.Tutorial.md) and you have an external table in DynamoDB named *ddb\_features*\. 

**Topics**
+ [Copying Data Using the Hive Default Format](#EMRforDynamoDB.CopyingData.HDFS.DefaultFormat)
+ [Copying Data with a User\-Specified Format](#EMRforDynamoDB.CopyingData.HDFS.UserSpecifiedFormat)
+ [Copying Data Without a Column Mapping](#EMRforDynamoDB.CopyingData.HDFS.NoColumnMapping)
+ [Accessing the Data in HDFS](#EMRforDynamoDB.CopyingData.HDFS.ViewingData)

## Copying Data Using the Hive Default Format<a name="EMRforDynamoDB.CopyingData.HDFS.DefaultFormat"></a>

**Example From DynamoDB to HDFS**  
Use an `INSERT OVERWRITE` statement to write directly to HDFS\.  

```
INSERT OVERWRITE DIRECTORY 'hdfs:///user/hadoop/hive-test'
SELECT * FROM ddb_features;
```
The data file in HDFS looks like this:  

```
920709^ASoldiers Farewell Hill^ASummit^ANM^A32.3564729^A-108.33004616135
1178153^AJones Run^AStream^APA^A41.2120086^A-79.25920781260
253838^ASentinel Dome^ASummit^ACA^A37.7229821^A-119.584338133
264054^ANeversweet Gulch^AValley^ACA^A41.6565269^A-122.83614322900
115905^AChacaloochee Bay^ABay^AAL^A30.6979676^A-87.97388530
```
Each field is separated by an SOH character \(start of heading, 0x01\)\. In the file, SOH appears as **^A**\.

**Example From HDFS to DynamoDB**  

1. Create an external table that maps to the unformatted data in HDFS\.

   ```
   CREATE EXTERNAL TABLE hdfs_features_unformatted
       (feature_id       BIGINT,
       feature_name      STRING ,
       feature_class     STRING ,
       state_alpha       STRING,
       prim_lat_dec      DOUBLE ,
       prim_long_dec     DOUBLE ,
       elev_in_ft        BIGINT)
   LOCATION 'hdfs:///user/hadoop/hive-test';
   ```

1. Copy the data to DynamoDB\.

   ```
   INSERT OVERWRITE TABLE ddb_features
   SELECT * FROM hdfs_features_unformatted;
   ```

## Copying Data with a User\-Specified Format<a name="EMRforDynamoDB.CopyingData.HDFS.UserSpecifiedFormat"></a>

If you want to use a different field separator character, you can create an external table that maps to the HDFS directory\. You might use this technique for creating data files with comma\-separated values \(CSV\)\.

**Example From DynamoDB to HDFS**  

1. Create a Hive external table that maps to HDFS\. When you do this, ensure that the data types are consistent with those of the DynamoDB external table\.

   ```
   CREATE EXTERNAL TABLE hdfs_features_csv
       (feature_id       BIGINT,
       feature_name      STRING ,
       feature_class     STRING ,
       state_alpha       STRING,
       prim_lat_dec      DOUBLE ,
       prim_long_dec     DOUBLE ,
       elev_in_ft        BIGINT)
   ROW FORMAT DELIMITED
   FIELDS TERMINATED BY ','
   LOCATION 'hdfs:///user/hadoop/hive-test';
   ```

1. Copy the data from DynamoDB\.

   ```
   INSERT OVERWRITE TABLE hdfs_features_csv
   SELECT * FROM ddb_features;
   ```
The data file in HDFS looks like this:  

```
920709,Soldiers Farewell Hill,Summit,NM,32.3564729,-108.3300461,6135
1178153,Jones Run,Stream,PA,41.2120086,-79.2592078,1260
253838,Sentinel Dome,Summit,CA,37.7229821,-119.58433,8133
264054,Neversweet Gulch,Valley,CA,41.6565269,-122.8361432,2900
115905,Chacaloochee Bay,Bay,AL,30.6979676,-87.9738853,0
```

**Example From HDFS to DynamoDB**  
With a single HiveQL statement, you can populate the DynamoDB table using the data from HDFS:  

```
INSERT OVERWRITE TABLE ddb_features
SELECT * FROM hdfs_features_csv;
```

## Copying Data Without a Column Mapping<a name="EMRforDynamoDB.CopyingData.HDFS.NoColumnMapping"></a>

You can copy data from DynamoDB in a raw format and write it to HDFS without specifying any data types or column mapping\. You can use this method to create an archive of DynamoDB data and store it in HDFS\.



**Note**  
If your DynamoDB table contains attributes of type Map, List, Boolean or Null, then this is the only way you can use Hive to copy data from DynamoDB to HDFS\.

**Example From DynamoDB to HDFS**  

1. Create an external table associated with your DynamoDB table\. \(There is no `dynamodb.column.mapping` in this HiveQL statement\.\)

   ```
   CREATE EXTERNAL TABLE ddb_features_no_mapping
       (item MAP<STRING, STRING>)
   STORED BY 'org.apache.hadoop.hive.dynamodb.DynamoDBStorageHandler'
   TBLPROPERTIES ("dynamodb.table.name" = "Features");
   ```

   

1. Create another external table associated with your HDFS directory\.

   ```
   CREATE EXTERNAL TABLE hdfs_features_no_mapping
       (item MAP<STRING, STRING>)
   ROW FORMAT DELIMITED
   FIELDS TERMINATED BY '\t'
   LINES TERMINATED BY '\n'
   LOCATION 'hdfs:///user/hadoop/hive-test';
   ```

1. Copy the data from DynamoDB to HDFS\.

   ```
   INSERT OVERWRITE TABLE hdfs_features_no_mapping
   SELECT * FROM ddb_features_no_mapping;
   ```
The data file in HDFS looks like this:  

```
Name^C{"s":"Soldiers Farewell Hill"}^BState^C{"s":"NM"}^BClass^C{"s":"Summit"}^BElevation^C{"n":"6135"}^BLatitude^C{"n":"32.3564729"}^BId^C{"n":"920709"}^BLongitude^C{"n":"-108.3300461"}
Name^C{"s":"Jones Run"}^BState^C{"s":"PA"}^BClass^C{"s":"Stream"}^BElevation^C{"n":"1260"}^BLatitude^C{"n":"41.2120086"}^BId^C{"n":"1178153"}^BLongitude^C{"n":"-79.2592078"}
Name^C{"s":"Sentinel Dome"}^BState^C{"s":"CA"}^BClass^C{"s":"Summit"}^BElevation^C{"n":"8133"}^BLatitude^C{"n":"37.7229821"}^BId^C{"n":"253838"}^BLongitude^C{"n":"-119.58433"}
Name^C{"s":"Neversweet Gulch"}^BState^C{"s":"CA"}^BClass^C{"s":"Valley"}^BElevation^C{"n":"2900"}^BLatitude^C{"n":"41.6565269"}^BId^C{"n":"264054"}^BLongitude^C{"n":"-122.8361432"}
Name^C{"s":"Chacaloochee Bay"}^BState^C{"s":"AL"}^BClass^C{"s":"Bay"}^BElevation^C{"n":"0"}^BLatitude^C{"n":"30.6979676"}^BId^C{"n":"115905"}^BLongitude^C{"n":"-87.9738853"}
```
Each field begins with an STX character \(start of text, 0x02\) and ends with an ETX character \(end of text, 0x03\)\. In the file, STX appears as **^B** and ETX appears as **^C**\.

**Example From HDFS to DynamoDB**  
With a single HiveQL statement, you can populate the DynamoDB table using the data from HDFS:  

```
INSERT OVERWRITE TABLE ddb_features_no_mapping
SELECT * FROM hdfs_features_no_mapping;
```

## Accessing the Data in HDFS<a name="EMRforDynamoDB.CopyingData.HDFS.ViewingData"></a>

HDFS is a distributed file system, accessible to all of the nodes in the Amazon EMR cluster\. If you use SSH to connect to the leader node, you can use command line tools to access the data that Hive wrote to HDFS\.

HDFS is not the same thing as the local file system on the leader node\. You cannot work with files and directories in HDFS using standard Linux commands \(such as `cat`, `cp`, `mv`, or `rm`\)\. Instead, you perform these tasks using the `hadoop fs` command\.

The following steps are written with the assumption you have copied data from DynamoDB to HDFS using one of the procedures in this section\.

1. If you are currently at the Hive command prompt, exit to the Linux command prompt\.

   ```
   hive> exit;
   ```

1. List the contents of the /user/hadoop/hive\-test directory in HDFS\. \(This is where Hive copied the data from DynamoDB\.\)

   ```
   hadoop fs -ls /user/hadoop/hive-test
   ```

   The response should look similar to this:

   ```
   Found 1 items
   -rw-r--r-- 1 hadoop hadoop 29504 2016-06-08 23:40 /user/hadoop/hive-test/000000_0
   ```

   The file name \(*000000\_0*\) is system\-generated\.

1. View the contents of the file:

   ```
   hadoop fs -cat /user/hadoop/hive-test/000000_0
   ```
**Note**  
In this example, the file is relatively small \(approximately 29 KB\)\. Be careful when you use this command with files that are very large or contain non\-printable characters\.

1. \(Optional\) You can copy the data file from HDFS to the local file system on the leader node\. After you do this, you can use standard Linux command line utilities to work with the data in the file\.

   ```
   hadoop fs -get /user/hadoop/hive-test/000000_0
   ```

   This command will not overwrite the file\.
**Note**  
The local file system on the leader node has limited capacity\. Do not use this command with files that are larger than the available space in the local file system\.