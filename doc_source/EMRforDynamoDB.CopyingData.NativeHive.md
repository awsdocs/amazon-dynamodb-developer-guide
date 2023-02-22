# Copying data between DynamoDB and a native Hive table<a name="EMRforDynamoDB.CopyingData.NativeHive"></a>

If you have data in a DynamoDB table, you can copy the data to a native Hive table\. This will give you a snapshot of the data, as of the time you copied it\. 

You might decide to do this if you need to perform many HiveQL queries, but do not want to consume provisioned throughput capacity from DynamoDB\. Because the data in the native Hive table is a copy of the data from DynamoDB, and not "live" data, your queries should not expect that the data is up\-to\-date\.

**Note**  
The examples in this section are written with the assumption you followed the steps in [Tutorial: Working with Amazon DynamoDB and Apache Hive](EMRforDynamoDB.Tutorial.md) and have an external table in DynamoDB named *ddb\_features*\. 

**Example From DynamoDB to native Hive table**  
You can create a native Hive table and populate it with data from *ddb\_features*, like this:  

```
CREATE TABLE features_snapshot AS
SELECT * FROM ddb_features;
```
You can then refresh the data at any time:  

```
INSERT OVERWRITE TABLE features_snapshot
SELECT * FROM ddb_features;
```
In these examples, the subquery `SELECT * FROM ddb_features` will retrieve all of the data from *ddb\_features*\. If you only want to copy a subset of the data, you can use a `WHERE` clause in the subquery\.  
The following example creates a native Hive table, containing only some of the attributes for lakes and summits:  

```
CREATE TABLE lakes_and_summits AS
SELECT feature_name, feature_class, state_alpha
FROM ddb_features
WHERE feature_class IN ('Lake','Summit');
```

**Example From native Hive table to DynamoDB**  
Use the following HiveQL statement to copy the data from the native Hive table to *ddb\_features*:  

```
INSERT OVERWRITE TABLE ddb_features
SELECT * FROM features_snapshot;
```