# IP Address Ranges<a name="Using.IPRanges"></a>

Amazon Web Services \(AWS\) publishes its current IP address ranges in JSON format\. To view the current ranges, download [ip\-ranges\.json](https://ip-ranges.amazonaws.com/ip-ranges.json)\. For more information, see [AWS IP Address Ranges](https://docs.aws.amazon.com/general/latest/gr/aws-ip-ranges.html) in the AWS General Reference\.

To find the IP address ranges that you can use to [access to DynamoDB tables and indexes](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Operations_Amazon_DynamoDB.html), search the ip\-ranges\.json file for the following string: `"service": "DYNAMODB"`\.

**Note**  
The IP address ranges do not apply to DynamoDB Streams or DynamoDB Accelerator \(DAX\)\. 