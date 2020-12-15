# Creating an External Table in Hive<a name="EMRforDynamoDB.ExternalTableForDDB"></a>

In [Tutorial: Working with Amazon DynamoDB and Apache Hive](EMRforDynamoDB.Tutorial.md), you created an external Hive table that mapped to a DynamoDB table\. When you issued HiveQL statements against the external table, the read and write operations were passed through to the DynamoDB table\.

You can think of an external table as a pointer to a data source that is managed and stored elsewhere\. In this case, the underlying data source is a DynamoDB table\. \(The table must already exist\. You cannot create, update, or delete a DynamoDB table from within Hive\.\) You use the `CREATE EXTERNAL TABLE` statement to create the external table\. After that, you can use HiveQL to work with data in DynamoDB, as if that data were stored locally within Hive\.

**Note**  
You can use `INSERT` statements to insert data into an external table and `SELECT` statements to select data from it\. However, you cannot use `UPDATE` or `DELETE` statements to manipulate data in the table\.

If you no longer need the external table, you can remove it using the `DROP TABLE` statement\. In this case, `DROP TABLE` only removes the external table in Hive\. It does not affect the underlying DynamoDB table or any of its data\.

**Topics**
+ [CREATE EXTERNAL TABLE Syntax](#EMRforDynamoDB.ExternalTableForDDB.Syntax)
+ [Data Type Mappings](#EMRforDynamoDB.ExternalTableForDDB.DataTypes)

## CREATE EXTERNAL TABLE Syntax<a name="EMRforDynamoDB.ExternalTableForDDB.Syntax"></a>

The following shows the HiveQL syntax for creating an external Hive table that maps to a DynamoDB table:

```
CREATE EXTERNAL TABLE hive_table
    (hive_column1_name hive_column1_datatype, hive_column2_name hive_column2_datatype...)
STORED BY 'org.apache.hadoop.hive.dynamodb.DynamoDBStorageHandler'
TBLPROPERTIES (
    "dynamodb.table.name" = "dynamodb_table",
    "dynamodb.column.mapping" = "hive_column1_name:dynamodb_attribute1_name,hive_column2_name:dynamodb_attribute2_name..."
);
```

Line 1 is the start of the `CREATE EXTERNAL TABLE` statement, where you provide the name of the Hive table \(*hive\_table*\) you want to create\.

Line 2 specifies the columns and data types for *hive\_table*\. You need to define columns and data types that correspond to the attributes in the DynamoDB table\. 

Line 3 is the `STORED BY` clause, where you specify a class that handles data management between the Hive and the DynamoDB table\. For DynamoDB, `STORED BY` should be set to `'org.apache.hadoop.hive.dynamodb.DynamoDBStorageHandler'`\. 

Line 4 is the start of the `TBLPROPERTIES` clause, where you define the following parameters for `DynamoDBStorageHandler`: 
+ `dynamodb.table.name`—the name of the DynamoDB table\.
+  `dynamodb.column.mapping`—pairs of column names in the Hive table and their corresponding attributes in the DynamoDB table\. Each pair is of the form *hive\_column\_name:dynamodb\_attribute\_name*, and the pairs are separated by commas\.

Note the following:
+ The name of the Hive table name does not have to be the same as the DynamoDB table name\.
+ The Hive table column names do not have to be the same as those in the DynamoDB table\.
+ The table specified by `dynamodb.table.name` must exist in DynamoDB\.
+ For `dynamodb.column.mapping`:
  + You must map the key schema attributes for the DynamoDB table\. This includes the partition key and the sort key \(if present\)\.
  + You do not have to map the non\-key attributes of the DynamoDB table\. However, you will not see any data from those attributes when you query the Hive table\.
  + If the data types of a Hive table column and a DynamoDB attribute are incompatible, you will see `NULL` in these columns when you query the Hive table\.

**Note**  
The `CREATE EXTERNAL TABLE` statement does not perform any validation on the `TBLPROPERTIES` clause\. The values you provide for `dynamodb.table.name` and `dynamodb.column.mapping` are only evaluated by the `DynamoDBStorageHandler` class when you attempt to access the table\.

## Data Type Mappings<a name="EMRforDynamoDB.ExternalTableForDDB.DataTypes"></a>

The following table shows DynamoDB data types and compatible Hive data types:


****  

| DynamoDB Data Type | Hive Data Type | 
| --- | --- | 
|  String  |  `STRING`  | 
|  Number  |  `BIGINT` or `DOUBLE`  | 
|  Binary  |  `BINARY`  | 
|  String Set  |  `ARRAY<STRING>`  | 
|  Number Set  |  `ARRAY<BIGINT>` or `ARRAY<DOUBLE>`  | 
|  Binary Set  |  `ARRAY<BINARY>`  | 

**Note**  
The following DynamoDB data types are not supported by the `DynamoDBStorageHandler` class, so they cannot be used with `dynamodb.column.mapping`:  
Map
List
Boolean
Null

If you want to map a DynamoDB attribute of type Number, you must choose an appropriate Hive type:
+ The Hive `BIGINT` type is for 8\-byte signed integers\. It is the same as the `long` data type in Java\.
+ The Hive `DOUBLE` type is for 8\-bit double precision floating point numbers\. It is the same as the `double` type in Java\.

If you have numeric data stored in DynamoDB that has a higher precision than the Hive data type you choose, then accessing the DynamoDB data could cause a loss of precision\. 

If you export data of type Binary from DynamoDB to \(Amazon S3\) or HDFS, the data is stored as a Base64\-encoded string\. If you import data from Amazon S3 or HDFS into the DynamoDB Binary type, you must ensure the data is encoded as a Base64 string\.