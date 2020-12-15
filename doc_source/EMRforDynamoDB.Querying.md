# Querying Data in DynamoDB<a name="EMRforDynamoDB.Querying"></a>

The following examples show some ways that you can use HiveQL to query data stored in DynamoDB\.

These examples refer to the *ddb\_features* table in the tutorial \([Step 5: Copy Data to DynamoDB](EMRforDynamoDB.Tutorial.CopyDataToDDB.md)\)\.

**Topics**
+ [Using Aggregate Functions](#EMRforDynamoDB.Querying.AggregateFunctions)
+ [Using the GROUP BY and HAVING Clauses](#EMRforDynamoDB.Querying.GroupByAndHaving)
+ [Joining Two DynamoDB tables](#EMRforDynamoDB.Querying.JoiningTwoTables)
+ [Joining Tables from Different Sources](#EMRforDynamoDB.Querying.JoiningTablesFromDifferentSources)

## Using Aggregate Functions<a name="EMRforDynamoDB.Querying.AggregateFunctions"></a>

HiveQL provides built\-in functions for summarizing data values\. For example, you can use the `MAX` function to find the largest value for a selected column\. The following example returns the elevation of the highest feature in the state of Colorado\.

```
SELECT MAX(elev_in_ft)
FROM ddb_features
WHERE state_alpha = 'CO';
```

## Using the GROUP BY and HAVING Clauses<a name="EMRforDynamoDB.Querying.GroupByAndHaving"></a>

You can use the `GROUP BY` clause to collect data across multiple records\. This is often used with an aggregate function such as `SUM`, `COUNT`, `MIN`, or `MAX`\. You can also use the `HAVING` clause to discard any results that do not meet certain criteria\.

The following example returns a list of the highest elevations from states that have more than five features in the *ddb\_features* table\.

```
SELECT state_alpha, max(elev_in_ft)
FROM ddb_features
GROUP BY state_alpha
HAVING count(*) >= 5;
```

## Joining Two DynamoDB tables<a name="EMRforDynamoDB.Querying.JoiningTwoTables"></a>

The following example maps another Hive table \(*east\_coast\_states*\) to a table in DynamoDB\. The `SELECT` statement is a join across these two tables\. The join is computed on the cluster and returned\. The join does not take place in DynamoDB\. 

Consider a DynamoDB table named EastCoastStates that contains the following data:

```
StateName       StateAbbrev

Maine           ME
New Hampshire   NH
Massachusetts   MA
Rhode Island    RI
Connecticut     CT
New York        NY
New Jersey      NJ
Delaware        DE
Maryland        MD
Virginia        VA
North Carolina  NC
South Carolina  SC
Georgia         GA
Florida         FL
```

Let's assume the table is available as a Hive external table named east\_coast\_states:

```
1. CREATE EXTERNAL TABLE ddb_east_coast_states (state_name STRING, state_alpha STRING)
2. STORED BY 'org.apache.hadoop.hive.dynamodb.DynamoDBStorageHandler'
3. TBLPROPERTIES ("dynamodb.table.name" = "EastCoastStates",
4. "dynamodb.column.mapping" = "state_name:StateName,state_alpha:StateAbbrev");
```

The following join returns the states on the East Coast of the United States that have at least three features:

```
SELECT ecs.state_name, f.feature_class, COUNT(*)
FROM ddb_east_coast_states ecs
JOIN ddb_features f on ecs.state_alpha = f.state_alpha
GROUP BY ecs.state_name, f.feature_class
HAVING COUNT(*) >= 3;
```

## Joining Tables from Different Sources<a name="EMRforDynamoDB.Querying.JoiningTablesFromDifferentSources"></a>

In the following example, s3\_east\_coast\_states is a Hive table associated with a CSV file stored in Amazon S3\. The *ddb\_features* table is associated with data in DynamoDB\. The following example joins these two tables, returning the geographic features from states whose names begin with "New\."

```
1. create external table s3_east_coast_states (state_name STRING, state_alpha STRING)
2. ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
3. LOCATION 's3://bucketname/path/subpath/';
```

```
SELECT ecs.state_name, f.feature_name, f.feature_class
FROM s3_east_coast_states ecs
JOIN ddb_features f
ON ecs.state_alpha = f.state_alpha
WHERE ecs.state_name LIKE 'New%';
```