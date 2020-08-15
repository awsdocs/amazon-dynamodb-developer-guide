# Reading Non\-Printable UTF\-8 Character Data<a name="EMRforDynamoDB.CopyingData.NonPrintableData"></a>

To read and write non\-printable UTF\-8 character data, you can use the `STORED AS SEQUENCEFILE` clause when you create a Hive table\. A SequenceFile is a Hadoop binary file format\. You need to use Hadoop to read this file\. The following example shows how to export data from DynamoDB into Amazon S3\. You can use this functionality to handle non\-printable UTF\-8 encoded characters\. 

```
1. CREATE EXTERNAL TABLE s3_export(a_col string, b_col bigint, c_col array<string>)
2. STORED AS SEQUENCEFILE
3. LOCATION 's3://bucketname/path/subpath/';
4. 
5. INSERT OVERWRITE TABLE s3_export SELECT *
6. FROM hiveTableName;
```