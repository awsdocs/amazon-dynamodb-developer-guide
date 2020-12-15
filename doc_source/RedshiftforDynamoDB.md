# Loading Data From DynamoDB Into Amazon Redshift<a name="RedshiftforDynamoDB"></a>



Amazon Redshift complements Amazon DynamoDB with advanced business intelligence capabilities and a powerful SQL\-based interface\. When you copy data from a DynamoDB table into Amazon Redshift, you can perform complex data analysis queries on that data, including joins with other tables in your Amazon Redshift cluster\.

In terms of provisioned throughput, a copy operation from a DynamoDB table counts against that table's read capacity\. After the data is copied, your SQL queries in Amazon Redshift do not affect DynamoDB in any way\. This is because your queries act upon a copy of the data from DynamoDB, rather than upon DynamoDB itself\.

Before you can load data from a DynamoDB table, you must first create an Amazon Redshift table to serve as the destination for the data\. Keep in mind that you are copying data from a NoSQL environment into a SQL environment, and that there are certain rules in one environment that do not apply in the other\. Here are some of the differences to consider:
+ DynamoDB table names can contain up to 255 characters, including '\.' \(dot\) and '\-' \(dash\) characters, and are case\-sensitive\. Amazon Redshift table names are limited to 127 characters, cannot contain dots or dashes and are not case\-sensitive\. In addition, table names cannot conflict with any Amazon Redshift reserved words\.
+ DynamoDB does not support the SQL concept of NULL\. You need to specify how Amazon Redshift interprets empty or blank attribute values in DynamoDB, treating them either as NULLs or as empty fields\.
+ DynamoDB data types do not correspond directly with those of Amazon Redshift\. You need to ensure that each column in the Amazon Redshift table is of the correct data type and size to accommodate the data from DynamoDB\.

Here is an example COPY command from Amazon Redshift SQL:

```
copy favoritemovies from 'dynamodb://my-favorite-movies-table'
credentials 'aws_access_key_id=<Your-Access-Key-ID>;aws_secret_access_key=<Your-Secret-Access-Key>'
readratio 50;
```

In this example, the source table in DynamoDB is `my-favorite-movies-table`\. The target table in Amazon Redshift is `favoritemovies`\. The `readratio 50` clause regulates the percentage of provisioned throughput that is consumed; in this case, the COPY command will use no more than 50 percent of the read capacity units provisioned for `my-favorite-movies-table`\. We highly recommend setting this ratio to a value less than the average unused provisioned throughput\.

For detailed instructions on loading data from DynamoDB into Amazon Redshift, refer to the following sections in the [https://docs.aws.amazon.com/redshift/latest/dg/](https://docs.aws.amazon.com/redshift/latest/dg/):
+ [Loading data from a DynamoDB table](https://docs.aws.amazon.com/redshift/latest/dg/t_Loading-data-from-dynamodb.html)
+ [The COPY command](https://docs.aws.amazon.com/redshift/latest/dg/r_COPY.html)
+ [COPY examples](https://docs.aws.amazon.com/redshift/latest/dg/r_COPY_command_examples.html)