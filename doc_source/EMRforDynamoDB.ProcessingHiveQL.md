# Processing HiveQL Statements<a name="EMRforDynamoDB.ProcessingHiveQL"></a>

Hive is an application that runs on Hadoop, which is a batch\-oriented framework for running MapReduce jobs\. When you issue a HiveQL statement, Hive determines whether it can return the results immediately or whether it must submit a MapReduce job\.

For example, consider the *ddb\_features* table \(from [Tutorial: Working with Amazon DynamoDB and Apache Hive](EMRforDynamoDB.Tutorial.md)\)\. The following Hive query prints state abbreviations and the number of summits in each:

```
SELECT state_alpha, count(*)
FROM ddb_features
WHERE feature_class = 'Summit'
GROUP BY state_alpha;
```

Hive does not return the results immediately\. Instead, it submits a MapReduce job, which is processed by the Hadoop framework\. Hive will wait until the job is complete before it shows the results from the query:

```
AK  2
AL  2
AR  2
AZ  3
CA  7
CO  2
CT  2
ID  1
KS  1
ME  2
MI  1
MT  3
NC  1
NE  1
NM  1
NY  2
OR  5
PA  1
TN  1
TX  1
UT  4
VA  1
VT  2
WA  2
WY  3
Time taken: 8.753 seconds, Fetched: 25 row(s)
```

## Monitoring and Canceling Jobs<a name="EMRforDynamoDB.MonitorAndCancelJob"></a>

When Hive launches a Hadoop job, it prints output from that job\. The job completion status is updated as the job progresses\. In some cases, the status might not be updated for a long time\. \(This can happen when you are querying a large DynamoDB table that has a low provisioned read capacity setting\.\)

If you need to cancel the job before it is complete, you can type **Ctrl\+C** at any time\.